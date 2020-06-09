currentBuild.displayName = "frontend-#"+currentBuild.number

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
    KUBECONFIG = "$JENKINS_HOME/config"
    project = 'frontend'
    hubUser = 'ernesen'
    imageTag = "${env.BUILD_NUMBER}.0"
  }
    
  //libraries {
  //      lib('jenkins-shared-library')
  //}
    
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
    stage('Kubectl Config view') {
      steps{
        sh "export KUBECONFIG"
        sh "kubectl apply -f  ./frontend_deployment.yaml"
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
