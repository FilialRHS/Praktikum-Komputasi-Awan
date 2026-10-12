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
            Write-Output "user=$($env:DOCKER_USER)"
            Write-Output "pass_length=$($env:DOCKER_PASS.Length)"

            $env:DOCKER_PASS | docker login -u $env:DOCKER_USER --password-stdin
            if ($LASTEXITCODE -ne 0) { throw "docker login gagal" }

            docker push "$($env:IMAGE_NAME):$($env:BUILD_NUMBER)"
            if ($LASTEXITCODE -ne 0) { throw "push gagal" }

            docker tag "$($env:IMAGE_NAME):$($env:BUILD_NUMBER)" "$($env:IMAGE_NAME):latest"
            docker push "$($env:IMAGE_NAME):latest"
            if ($LASTEXITCODE -ne 0) { throw "push latest gagal" }
          '''
        }
      }
    }
  }

  post {
    always { echo 'Selesai build' }
  }
}
