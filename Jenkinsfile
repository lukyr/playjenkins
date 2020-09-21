pipeline {

  environment {
    registry = "datahubid/web"
    dockerImage = ""
  }

  agent {
    kubernetes {
      label 'jenkins-slaves'
      defaultContainer 'jnlp'
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
       environment {
        DOCKERHUB_CREDS = credentials('dockerhub')
      }
      steps {
        container('docker') {
          // Build new image
          // Publish new image
          sh "docker login --username $DOCKERHUB_CREDS_USR --password $DOCKERHUB_CREDS_PSW && docker push ${registry}:${env.GIT_COMMIT}"
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