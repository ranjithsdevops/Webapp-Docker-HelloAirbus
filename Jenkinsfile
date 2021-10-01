node{
    
    def buildNumber = BUILD_NUMBER
    stage("Git Clone"){
        git url: 'https://github.com/ranjithsdevops/Webapp-Docker-HelloAirbus.git', branch: 'main'
    }
        stage("Buid Docker Image"){
        sh "docker build -t ranjith8123/dockernodewebapp ."
    }
    stage("Push Image to DockerHub"){
        withCredentials([string(credentialsId: 'Docker_Hub_pwd', variable: 'Docker_Hub_pass')]) {
        sh "docker login -u ranjith8123 -p ${Docker_Hub_pass}"
        }
        sh "docker push ranjith8123/dockernodewebapp"
    }
    stage("Final Deploy App as Docker Container in Server"){
        def dockerRun = ' docker run  -d -p 49160:8080 --name dockernodewebapp ranjith8123/dockernodewebapp'
        sshagent(['docker_new_ssh']) {
          sh 'ssh -o StrictHostKeyChecking=no ubuntu@172.31.7.201 docker stop dockernodewebapp || true'
          sh 'ssh  ubuntu@172.31.7.201 docker rm dockernodewebapp || true'
          sh 'ssh  ubuntu@172.31.7.201 docker rmi -f  $(docker images -q) || true'
          sh "ssh  ubuntu@172.31.7.201 ${dockerRun}"
          sh "ssh ubuntu@172.31.7.201 curl -i localhost:49161"
        }
    }
    stage("Slack notification"){
        slackSend channel: 'jenkins-notification', message: 'Job succeeded', notifyCommitters: true, tokenCredentialId: 'slackCred'
    }
}