pipeline {
  agent any

  tools {
    jdk 'jdk-22'
    maven 'MAVEN_HOME'
  }

  environment {
    registry = "demo520/my-twitter-app"
    registryCredential = 'docker-hub-credentials'
    dockerImage = ''
  }

  stages {
    stage('Compile') {
      steps {
        sh 'mvn clean package'
      }
    }

    stage('Build Docker Image') {
      steps {
        script {
          sh 'docker context use default || true'
          dockerImage = docker.build("${registry}:${BUILD_NUMBER}")
        }
      }
    }

    stage('Push Image To Docker Hub') {
      steps {
        script {
          docker.withRegistry('https://registry.hub.docker.com', registryCredential) {
            dockerImage.push("${BUILD_NUMBER}")
            dockerImage.push("latest")
          }
        }
      }
    }

    stage('Deploy to Kubernetes') {
      steps {
        sh 'kubectl apply -f deployment.yml'
      }
    }
  }
}
