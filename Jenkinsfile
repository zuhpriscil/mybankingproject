pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "zuhpriscil/mybankingproject"
        DOCKER_TAG = "${BUILD_NUMBER}"
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }
stage('Build Jar') {
    steps {
        sh 'chmod +x mvnw'
        sh './mvnw clean package -DskipTests'
    }
}

        stage('Docker Build') {
            steps {
                sh '''
                    docker build -t ${DOCKER_IMAGE}:${DOCKER_TAG} .
                    docker tag ${DOCKER_IMAGE}:${DOCKER_TAG} ${DOCKER_IMAGE}:latest
                '''
            }
        }

        stage('Docker Push') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-creds',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh '''
                        echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
                        docker push ${DOCKER_IMAGE}:${DOCKER_TAG}
                        docker push ${DOCKER_IMAGE}:latest
                        docker logout
                    '''
                }
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                    docker rm -f demo-container || true
                    docker pull ${DOCKER_IMAGE}:${DOCKER_TAG}
                    docker run -d --name demo-container -p 8080:8080 ${DOCKER_IMAGE}:${DOCKER_TAG}
                '''
            }
        }
    }
}