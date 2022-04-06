pipeline {
  agent {
    kubernetes {
      yaml '''
        apiVersion: v1
        kind: Pod
        metadata:
          name: gcc-agent
          labels:
            role: compiler
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
          sh 'make'
        }
      }
    }
    stage('Test') {
      steps {
        container('gcc') {
          sh 'make test'
        }
      }
    }
  }
}
