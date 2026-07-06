pipeline {

    environment {
        IMAGE_NAME = 'bbcsite'
        CONTAINER_NAME = 'bbccontainer'
        MANIFEST_FILE = 'pod.yaml'
        NAMESPACE = 'bbcnews-ns'
    }

    stages {

        stage('Checkout Source') {
            agent {
                label 'dock'
            }
            steps {
                git branch: 'main',
                    url: 'https://github.com/famidha2004/Nodejs-01.git'
            }
        }

        stage('Build Docker Image') {
            agent {
                label 'dock'
            }
            steps {
                sh '''
                docker build -t ${IMAGE_NAME}:latest .
                '''
            }
        }

        stage('Stop Old Container') {
            agent {
                label 'dock'
            }
            steps {
                sh '''
                docker stop ${CONTAINER_NAME} || true
                docker rm ${CONTAINER_NAME} || true
                '''
            }
        }

        stage('Run Docker Container') {
            agent {
                label 'dock'
            }
            steps {
                sh '''
                docker run -d --name ${CONTAINER_NAME} -p 80:80 ${IMAGE_NAME}:latest
                docker ps
                '''
            }
        }

        stage('Push Docker Image') {
            agent {
                label 'dock'
            }
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'Dockerhub',
                        usernameVariable: 'DOCKER_USER',
                        passwordVariable: 'DOCKER_PASS'
                    )
                ]) {
                    sh '''
                    echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin

                    docker tag ${IMAGE_NAME}:latest famidha/${IMAGE_NAME}:latest

                    docker push famidha/${IMAGE_NAME}:latest

                    docker logout
                    '''
                }
            }
        }

        stage('Checkout Kubernetes Files') {
            agent {
                label 'kuber'
            }
            steps {
                git branch: 'main',
                    url: 'https://github.com/famidha2004/Nodejs-01.git'
            }
        }

        stage('Verify Kubernetes Tools') {
            agent {
                label 'kuber'
            }
            steps {
                sh '''
                eksctl version
                kubectl version --client
                '''
            }
        }

        stage('Deploy to Kubernetes') {
            agent {
                label 'kuber'
            }
            steps {
                sh '''
                kubectl apply -f ${MANIFEST_FILE}
                '''
            }
        }

        stage('Verify Deployment') {
            agent {
                label 'kuber'
            }
            steps {
                sh '''
                kubectl get ns

                kubectl get deployments -n ${NAMESPACE}

                kubectl get pods -n ${NAMESPACE} -o wide

                kubectl get svc -n ${NAMESPACE}

                kubectl describe namespace ${NAMESPACE}
                '''
            }
        }
    }
}
