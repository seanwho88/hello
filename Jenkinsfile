pipeline {
  agent {
    kubernetes {
      inheritFrom 'jenkins-agent'
    }
  }
  stages {
    stage('Build') {
      steps {
        container('gcc') {
          sh 'make clean'
          sh 'make hello'
        }
      }
    }
    stage('Test') {
      steps {
        container('gcc') {
          sh 'make hello'
          sh 'make test'
        }
      }
    }
  }
}
