currentBuild.displayName = "frontend-#"+currentBuild.number

pipeline {
  environment {
    DOCKER_TAG = getDockerTag()
    registry = "ernesen/frontend"
    registryCredential = 'DockerCredentials'
    dockerImage = ''
    KUBECONFIG = "$JENKINS_HOME/config"
    BUILD_NUMBER_V = ''
    project = 'frontend'
    hubUser = 'ernesen'
    ImageTag = "${env.BUILD_NUMBER}"
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
/*
    stage('Building image') {
      steps{
        script {
          dockerImage = docker.build registry + ":$BUILD_NUMBER.0"
        }
      }
    }

    stage('Deploy Image') {
      steps{
        script {
          docker.withRegistry( '', registryCredential ) {
            dockerImage.push()
            dockerImage = docker.build registry + ":latest"
            dockerImage.push()
          }
        }
      }
    }
    stage('Remove Unused docker image') {
      steps{
        sh "docker rmi $registry:$BUILD_NUMBER.0"
      }
    }
 */
     stage('dockerBuild') {
      steps{
        dockerBuild(project, hubUser,ImageTag, registryCredential)
      }
    }
     stage('dockerCleanup') {
      steps{
        dockerCleanup(project, hubUser, ImageTag)
        //sh "echo dockerCleanup"
      }
    }    
    stage('Kubectl Config view') {
      steps{
        sh "export KUBECONFIG"
        //sh "kubectl version --short"
        //sh "kubectl config get-contexts"
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

def dockerBuild(String project, String hubUser, String ImageTag, String registryCredential) {
    sh "docker image build -t ${hubUser}/${project} ."
    sh "docker tag ${hubUser}/${project} ${hubUser}/${project}:${ImageTag}"
    sh "docker tag ${hubUser}/${project} ${hubUser}/${project}:latest"
    withCredentials([usernamePassword(
            credentialsId: "${registryCredential}",
            usernameVariable: "USER",
            passwordVariable: "PASS"
    )]) {
        sh "docker login -u '$USER' -p '$PASS'"
    }
    sh "docker image push ${hubUser}/${project}:${ImageTag}"
    sh "docker image push ${hubUser}/${project}:latest"
}

def dockerCleanup(String project, String hubUser, String ImageTag) {
    sh "docker rmi ${hubUser}/${project}:${ImageTag}"
    sh "docker rmi ${hubUser}/${project}:latest"
}

def gitCheckout(Map stageParams) {
 
    checkout([
        $class: 'GitSCM',
        branches: [[name:  stageParams.branch ]],
        userRemoteConfigs: [[ url: stageParams.url ]]
    ])
  }
