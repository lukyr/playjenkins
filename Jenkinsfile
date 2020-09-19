pipeline {

  environment {
    registry = "192.168.0.105:5000/lukyr/web"
    dockerImage = ""
  }

  agent { label 'jenkins-slave' }

  stages {

    stage('Checkout Source') {
      steps {
        git 'https://github.com/lukyr/playjenkins.git'
      }
    }

    stage('Build image') {
      steps{
        script {
          dockerImage = docker.build registry + ":$BUILD_NUMBER"
        }
      }
    }

    stage('Push Image') {
      steps{
        script {
          docker.withRegistry( "" ) {
            dockerImage.push()
          }
        }
      }
    }

    stage('Deploy App') {
      steps {
        script {
          kubernetesDeploy(configs: "deployment.yaml", kubeconfigId: "mykubeconfig")
        }
      }
    }

  }

}