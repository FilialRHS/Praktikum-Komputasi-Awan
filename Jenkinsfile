pipeline {
  agent any

  environment {
    IMAGE_NAME = 'frhs06/simple-app'
    REGISTRY_CREDENTIALS = 'dockerhub-credentials'
  }

  stages {
    stage('Checkout') {
      steps { checkout scm }
    }

    stage('Build') {
      steps { bat 'echo "Build di Windows"' }
    }

    stage('Build Docker Image') {
      steps { bat "docker build -t %IMAGE_NAME%:%BUILD_NUMBER% ." }
    }

    stage('Push Docker Image') {
      steps {
        withCredentials([usernamePassword(credentialsId: REGISTRY_CREDENTIALS,
            usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
          powershell '''
            $env:DOCKER_PASS | docker login -u $env:DOCKER_USER --password-stdin
            if ($LASTEXITCODE -ne 0) { exit 1 }
            docker push "$env:IMAGE_NAME`:$env:BUILD_NUMBER"
            docker tag "$env:IMAGE_NAME`:$env:BUILD_NUMBER" "$env:IMAGE_NAME`:latest"
            docker push "$env:IMAGE_NAME`:latest"
          '''
        }
      }
    }
  }

  post {
    always { echo 'Selesai build' }
  }
}
