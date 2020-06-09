currentBuild.displayName = "frontend-#"+currentBuild.number

//@Library('jenkins-shared-library')_

pipeline {
    libraries {
        lib('jenkins-shared-library')
    }
    agent {
        docker {
           image 'ernesen/migratecf:3.0'
        }
    }
    stages {
        stage('Build') { 
            steps {
                dockerVersion() 
            }
        }
    }
}
/*
currentBuild.displayName = "frontend-#"+currentBuild.number

/*
library(
  identifier: 'jenkins-shared-library@1.0.4',
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
*/
/*
def getDockerTag(){
    def tag  = sh script: 'git rev-parse HEAD', returnStdout: true
    return tag
}

def getContext(environment) {
    return (env.BRANCH_NAME == 'master') ? environment : 'dev'
}

def notify(status){
    emailext (
      to: "ernese@sg.ibm.com",
      subject: "${status}: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'",
      body: """<p>${status}: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]':</p>
        <p>Check console output at <a href='${env.BUILD_URL}'>${env.JOB_NAME} [${env.BUILD_NUMBER}]</a></p>""",
    )
}

def dockerBuild(String project, String hubUser, String imageTag, String registryCredential) {
    sh "docker image build -t ${hubUser}/${project} ."
    sh "docker tag ${hubUser}/${project} ${hubUser}/${project}:${imageTag}"
    sh "docker tag ${hubUser}/${project} ${hubUser}/${project}:latest"
    withCredentials([usernamePassword(
            credentialsId: "${registryCredential}",
            usernameVariable: "USER",
            passwordVariable: "PASS"
    )]) {
        sh "docker login -u '$USER' -p '$PASS'"
    }
    sh "docker image push ${hubUser}/${project}:${imageTag}"
    sh "docker image push ${hubUser}/${project}:latest"
}

def dockerCleanup(String project, String hubUser, String imageTag) {
    sh "docker rmi ${hubUser}/${project}:${imageTag}"
    sh "docker rmi ${hubUser}/${project}:latest"
}

def gitCheckout(Map stageParams) { 
    checkout([
        $class: 'GitSCM',
        branches: [[name:  stageParams.branch ]],
        userRemoteConfigs: [[ url: stageParams.url ]]
    ])
  }
*/
def dockerVersion(){
    sh "docker version"   
}
