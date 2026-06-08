pipeline {
  agent {
    kubernetes {
      yamlFile 'agent.yaml'
    }
  }

  environment {
    IMAGE_NAME = 'sebasdockers/tarea-final'
    APP_VERSION = 'sebastian-jerez'
    NAMESPACE = 'ns-sebastian-jerez'
  }

  stages {
    stage('install') {
      steps {
        container('node') {
          sh 'npm install'
        }
      }
    }

    stage('test') {
      steps {
        container('node') {
          sh 'npm test'
        }
      }
    }

    stage('build') {
      steps {
        container('docker') {
          sh 'docker build -t $IMAGE_NAME:$APP_VERSION .'
        }
      }
    }

    stage('push') {
      steps {
        container('docker') {
          withCredentials([usernamePassword(credentialsId: 'dockerhub-sebasdockers', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
            sh 'echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin'
            sh 'docker push $IMAGE_NAME:$APP_VERSION'
          }
        }
      }
    }

    stage('deploy') {
      steps {
        container('kubectl') {
          sh 'kubectl apply -f entrega.yaml'
          sh 'kubectl rollout status deployment/app-sebastian-jerez -n $NAMESPACE'
        }
      }
    }
  }
}