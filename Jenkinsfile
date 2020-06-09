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
    //docker_image = 'ernesen/migratecf:3.0'
    kube_file = './frontend_deployment.yaml'
  }
    
  agent {
    docker { image 'ernesen/migratecf:3.0' }
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
        kubeDeploy("./frontend_deployment.yaml")
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
