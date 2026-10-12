```groovy
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
            if ($env:DOCKER_USER -ne "frhs06") {
              Write-Host "Username Jenkins tidak sesuai"
              exit 1
            }

            if ([string]::IsNullOrWhiteSpace($env:DOCKER_PASS)) {
              Write-Host "Password Jenkins kosong"
              exit 1
            }

            Write-Host "Username sesuai dan password tersedia"
          '''
        }
      }
    }
  }
}
```
