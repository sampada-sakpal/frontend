def PROJECT = "frontend"

currentBuild.displayName = "$PROJECT-#"+currentBuild.number

library(
  identifier: 'jenkins-shared-library@v1.0',
  retriever: modernSCM(
    [
      $class: 'GitSCMSource',
      remote: 'https://github.com/ernesen/jenkins-shared-library.git'
    ]
  )
)

pipeline {
    environment {
    registryCredential = 'DockerCredentials'
    project = "$PROJECT"
    hubUser = 'ernesen'
    imageTag = "${env.BUILD_NUMBER}.0"
    DOCKER_IMAGE = 'ernesen/migratecf:3.0'
    KUBE_FILE = "./frontend_deployment.yaml"
  }
    
  agent {
    docker { image DOCKER_IMAGE }
  }

  stages {    
    stage('Confirm') {
       steps{
        script {
           slackSend (color: '#00FF00', message: "SUCCESSFUL: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
           notify('Confirm')
         }
       }
    }

     stage('dockerBuild') {
      steps{
        dockerBuild(project, hubUser,imageTag, registryCredential)
      }
    }
     stage('dockerCleanup') {
      steps{
        dockerCleanup(project, hubUser, imageTag)
      }
    }    
    stage('Kubectl deploy') {
      steps{       
        kubectlApply(KUBE_FILE)
      }
    }
    stage('Helm Config view') {
      steps{
        sh "helm version --short"
      }
    }
    stage('Send Slack notifications') {
      steps{
        slackSend (color: '#00FF00', message: "SUCCESS: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
        notify('Send Slack notifications')
      }
    }
  }
}
