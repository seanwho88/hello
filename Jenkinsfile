pipeline {
  agent {
    kubernetes {
      yaml '''
        apiVersion: v1
        kind: Pod
        metadata:
          name: gcc
          labels:
            app: gcc
        spec:
          containers:
          - name: gcc
            image: registry:5000/gcc:latest
            command:
            - cat
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
