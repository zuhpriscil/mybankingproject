pipeline {
  agent any

  environment {
    DOCKER_IMAGE = "zuhrpsicl/mybankingproject"
    DOCKER_TAG   = "${env.BUILD_NUMBER}"
    INVENTORY    = "inventory.ini"
    PLAYBOOK     = "deploy_app.yml"
  }

  stages {

    stage('Checkout') {
      steps {
        checkout scm
      }
    }

    stage('Build + Test (Maven in Docker)') {
      steps {
        sh '''
          set -e
          docker run --rm \
            -v "$PWD":/app \
            -w /app \
            maven:3.9.6-eclipse-temurin-17 \
            mvn -B clean test package
        '''
      }
    }

    stage('Docker Build') {
      steps {
        sh '''
          set -e
          docker build -t ${DOCKER_IMAGE}:${DOCKER_TAG} .
          docker tag ${DOCKER_IMAGE}:${DOCKER_TAG} ${DOCKER_IMAGE}:latest
        '''
      }
    }

    stage('Docker Push') {
      steps {
        withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'DH_USER', passwordVariable: 'DH_PASS')]) {
          sh '''
            set -e
            echo "$DH_PASS" | docker login -u "$DH_USER" --password-stdin
            docker push ${DOCKER_IMAGE}:${DOCKER_TAG}
            docker push ${DOCKER_IMAGE}:latest
          '''
        }
      }
    }

    stage('Deploy (Ansible)') {
      steps {
        withCredentials([
          sshUserPrivateKey(credentialsId: 'ec2-ssh-key', keyFileVariable: 'SSH_KEY', usernameVariable: 'SSH_USER')
        ]) {
          sh '''
            set -e

            # Run Ansible from a container so Jenkins doesn't need ansible installed
            docker run --rm \
              -v "$PWD":/work \
              -w /work \
              -v "$SSH_KEY":/key.pem:ro \
              cytopia/ansible:latest \
              ansible-playbook -i ${INVENTORY} ${PLAYBOOK} \
                --user ${SSH_USER} \
                --private-key /key.pem
          '''
        }
      }
    }
  }

  post {
    always {
      sh 'docker logout || true'
    }
  }
}
