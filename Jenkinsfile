pipeline {
  agent any

  environment {
    IMAGE = "zuhpriscil/mybankingproject"
  }

  stages {
    stage('Checkout') {
      steps { checkout scm }
    }

    stage('Maven Build (inside Docker)') {
      steps {
        sh '''
          docker run --rm \
            -v "$PWD":/app \
            -w /app \
            maven:3.9.6-eclipse-temurin-17 \
            mvn -B -DskipTests clean package
        '''
      }
    }

    stage('Docker Build') {
      steps {
        sh 'docker build -t ${IMAGE}:${BUILD_NUMBER} .'
        sh 'docker tag ${IMAGE}:${BUILD_NUMBER} ${IMAGE}:latest'
      }
    }
  }
}


