pipeline {
  agent any
  tools {
    jdk 'JDK17'
  }
  stages {
    stage('Build') {
      steps {
        sh 'chmod +x mvnw || true'
        sh './mvnw clean package -DskipTests'
      }
    }
  }
}