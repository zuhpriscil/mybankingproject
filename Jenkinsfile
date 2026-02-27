pipeline {
    agent any

    environment {
        IMAGE = "zuhpriscil/mybankingproject"
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Maven Build') {
            steps {
                sh 'mvn -v'
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Docker Build') {
            steps {
                sh 'docker --version'
                sh "docker build -t ${IMAGE}:${BUILD_NUMBER} ."
                sh "docker tag ${IMAGE}:${BUILD_NUMBER} ${IMAGE}:latest"
            }
        }
    }
}