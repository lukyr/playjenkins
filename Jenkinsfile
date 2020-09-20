pipeline {

  environment {
    registry = "192.168.0.105:5000/lukyr/web"
    dockerImage = ""
  }

  agent {
    kubernetes {
      label 'jenkins-slaves'
      }
    }

  stages {

    stage('Checkout Source') {
      steps {
        git 'https://github.com/lukyr/playjenkins.git'
      }
    }

    stage('Build Image') {
      steps {
        container('docker') {
          // Build new image
          sh "until docker ps; do sleep 3; done && docker build -t ${registry}:${env.GIT_COMMIT} ."
        }
      }
    }

    stage('Push Image') {
      steps {
        container('docker') {
          // Build new image
          // Publish new image
          sh "docker push ${registry}:${env.GIT_COMMIT}"
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