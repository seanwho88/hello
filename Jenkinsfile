pipeline {
    agent none 
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
            agent {
                kubernetes {
                    inheritFrom 'agent-template'
                }
            }
            steps{
                container('docker') {
                    sh 'echo $DOCKER_TOKEN | docker login --username $DOCKER_USER --password-stdin'
                    sh 'docker build -t $DOCKER_REGISTRY:$BUILD_NUMBER .'
                    sh 'docker push $DOCKER_REGISTRY:$BUILD_NUMBER'
                }
            }
        }
        stage ('Deploy') {
            agent {
                kubernetes {
                    inheritFrom 'agent-template'
                }
            }
            steps {
                container('kube-agent') {
                    sh 'mkdir $HOME/.kube'
                    sh 'cp /etc/kubernetes/admin.conf $/.kube/config'
                    sh 'export IMAGE=$DOCKER_REGISTRY'
                    sh 'envsubst < deployment.yml | kubectl apply -n jenkins -f -'
                    sh 'kubectl -f service.yml -n jenkins'
                }
            }
        }
    }
}
