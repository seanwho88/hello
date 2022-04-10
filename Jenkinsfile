pipeline {
    agent any 
    environment {
        registry = "linhbngo/go_server"
        GOCACHE = "/tmp"
    }
    stages {
        stage('Build') {
            agent {
                kubernetes {
                    inheritFrom 'agent-template'
                }
            }
            steps {
                container('golang') {
                    // Create our project directory.
                    sh 'cd ${GOPATH}/src'
                    sh 'mkdir -p ${GOPATH}/src/hello-world'
                    // Copy all files in our Jenkins workspace to our project directory.                
                    sh 'cp -r ${WORKSPACE}/* ${GOPATH}/src/hello-world'
                    // Build the app.
                    sh 'export GO111MODULE=auto; go build'  
                }
            }     
        }
        stage('Test') {
            agent {
                kubernetes {
                    inheritFrom 'agent-template'
                }
            }
            steps {
                container('golang') {                 
                    // Create our project directory.
                    sh 'cd ${GOPATH}/src'
                    sh 'mkdir -p ${GOPATH}/src/hello-world'
                    // Copy all files in our Jenkins workspace to our project directory.                
                    sh 'cp -r ${WORKSPACE}/* ${GOPATH}/src/hello-world'
                    // Remove cached test results.
                    sh 'go clean -cache'
                    // Run Unit Tests.
                    sh 'export GO111MODULE=auto; go test ./... -v -short'            
                }
            }
        }
        stage('Publish') {
            environment {
                registryCredential = 'dockerhub'
            }
            steps{
                script {
                    def appimage = docker.build registry + ":$BUILD_NUMBER"
                    docker.withRegistry( '', registryCredential ) {
                        appimage.push()
                        appimage.push('latest')
                    }
                }
            }
        }
        stage ('Deploy') {
            steps {
                script{
                    def image_id = registry + ":$BUILD_NUMBER"
                    sh "export IMAGE=${image+id}; envsubst < deployment.yaml | kubectl apply -n jenkins -f -"
                    sh "kubectl -f service.yml -n jenkins"
                }
            }
        }
    }
}
