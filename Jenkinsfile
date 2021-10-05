node {
  try {
    notifyStarted()

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
        slackSend channel: 'jenkins-notification', color: '#FFFF00', message: "DockerImage pushed to DockerHub", tokenCredentialId: 'slackCred'
    }
    stage("Final Deploy App as Docker Container in Server"){
        def dockerRun = ' docker run  -d -p 49160:8080 --name dockernodewebapp ranjith8123/dockernodewebapp'
        sshagent(['docker_new_ssh']) {
          sh 'ssh -o StrictHostKeyChecking=no ubuntu@172.31.7.201 docker stop dockernodewebapp || true'
          sh 'ssh  ubuntu@172.31.7.201 docker rm dockernodewebapp || true'
          sh 'ssh  ubuntu@172.31.7.201 docker rmi -f  $(docker images -q) || true'
          sh "ssh  ubuntu@172.31.7.201 ${dockerRun}"
          sh "ssh ubuntu@172.31.7.201 curl -i localhost:49160"
        }
        slackSend channel: 'jenkins-notification', color: '#00FF00', message: "Curl Successful", tokenCredentialId: 'slackCred' 
    }

    notifySuccessful()
  } catch (e) {
    currentBuild.result = "FAILED"
    notifyFailed()
    throw e
  }
}

def notifyStarted() { 
    slackSend channel: 'jenkins-notification', color: '#FFFF00', message: "Build Started - Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'", tokenCredentialId: 'slackCred' 
 }

def notifySuccessful() { 
    slackSend channel: 'jenkins-notification', color: '#00FF00', message: "BUILD SUCCESSFUL - Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'", tokenCredentialId: 'slackCred' 
    }

def notifyFailed() {
    slackSend channel: 'jenkins-notification', color: '#FF0000', message: "BUILD FAILED; Please take the immediate action; who broke the chain? - Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'", tokenCredentialId: 'slackCred' 
}  