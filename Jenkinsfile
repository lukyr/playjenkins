pipeline {

  environment {
    registry = "192.168.0.105:5000/lukyr/web"
    dockerImage = ""
  }

  agent {
    kubernetes {
      label 'jenkins-slaves'
      defaultContainer 'jnlp'
      yaml """
apiVersion: v1
kind: Pod
spec:
  containers:
  - name: dind
    image: docker:18.09-dind
    securityContext:
      privileged: true
  - name: docker
    env:
    - name: DOCKER_HOST
      value: 127.0.0.1
    image: docker:18.09
    command:
    - cat
    tty: true
  - name: jnlp
    image: jenkins/jnlp-slave:latest
    command:
    - cat
    tty: true
"""
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