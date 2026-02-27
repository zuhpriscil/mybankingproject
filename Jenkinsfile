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
                bat 'mvn -v'
                bat 'mvn clean package -DskipTests'
            }
        }

        stage('Docker Build') {
            steps {
                bat 'docker --version'
                bat "docker build -t %IMAGE%:%BUILD_NUMBER% ."
                bat "docker tag %IMAGE%:%BUILD_NUMBER% %IMAGE%:latest"
            }
        }
    }
}