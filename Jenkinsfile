pipeline {
  agent {
    kubernetes {
      inheritFrom 'jenkins-agent'
      yaml '''
      spec:
        containers:
        - name: gcc
          image: registry:5000/gcc:latest
      '''
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
