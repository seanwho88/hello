pipeline {
  agent {
    kubernetes {
      yaml '''
      spec:
      containers:
      - name: gcc
        image: registry:5000/gcc:latest
        command:
        - sleep
        args:
        - 99d
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
