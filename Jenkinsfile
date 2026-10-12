pipeline {
  agent any

  environment {
    IMAGE_NAME = 'frhs06/simple-app'
    REGISTRY_CREDENTIALS = 'dockerhub-credentials'
  }

  stages {
    stage('Checkout') {
      steps {
        checkout scm
      }
    }

    stage('Build') {
      steps {
        bat 'echo "Build di Windows"'
      }
    }

    stage('Build Docker Image') {
      steps {
        bat 'docker build -t %IMAGE_NAME%:%BUILD_NUMBER% .'
      }
    }

    stage('Push Docker Image') {
      steps {
        withCredentials([usernamePassword(
          credentialsId: 'dockerhub-credentials',
          usernameVariable: 'DOCKER_USER',
          passwordVariable: 'DOCKER_PASS'
        )]) {
          powershell '''
            $env:DOCKER_PASS | docker login -u $env:DOCKER_USER --password-stdin
            if ($LASTEXITCODE -ne 0) {
              exit $LASTEXITCODE
            }
          '''

          bat 'docker push %IMAGE_NAME%:%BUILD_NUMBER%'

          bat 'docker tag %IMAGE_NAME%:%BUILD_NUMBER% %IMAGE_NAME%:latest'

          bat 'docker push %IMAGE_NAME%:latest'
        }
      }
    }
  }
}
