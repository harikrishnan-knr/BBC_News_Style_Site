pipeline{
    environment{
	IMAGE_NAME = 'bbcsite'
	CONTAINER_NAME = 'newsapp'
	POD_NAME = 'pod.yaml'
	SERVICE_NAME = ''
                    }

	stages{
	stage('checkout'){
		agent{
		label 'dock'
	}
	steps{
	git branch: 'main', url: 'https://github.com/famidha2004/Nodejs-01.git'

                    }
    }
	stage('build'){
		agent{
		label 'dock'
	}
	steps{
	sh 'docker build -t ${IMAGE_NAME}:latest .'
                    }
    }
	stage('stop old containers'){
		agent{
		label 'dock'
	}
	steps{
	sh 'docker stop ${CONTAINER_NAME} || true'
	sh 'docker rm ${CONTAINER_NAME} || true'
	}
}
	stage('docker image run'){
		agent{
		label 'dock'
	}
	steps{
	sh 'docker run -d --name ${CONTAINER_NAME} -p 80:80 ${IMAGE_NAME}:latest'
	sh 'docker ps'
	}
}
		stage('docker push'){
			agent{
		label 'dock'
	}
			steps{
				withCredentials([usernamePassword(
                    credentialsId: 'Dockerhub',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {

                    sh '''
                    echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
					docker tag ${IMAGE_NAME}:latest famidha/${IMAGE_NAME}:latest
                    docker push famidha/${IMAGE_NAME}:latest
                    docker logout
                    '''
                }
            }
}
stage('checkout'){
agent{
	label 'kuber'
}
	steps{
	git branch: 'main', url: 'https://github.com/famidha2004/Nodejs-01.git'

                    }
    }

stage('checking the version of eksctl and kubernets'){
	agent{
		label 'kuber'
	}
	steps{
		sh '''eksctl version
			kubectl version'''
	}	
}
stage('apply manifest'){
	agent{
		label 'kuber'
	}
	steps{
		sh '''kubectl delete -f ${POD_NAME) || true
		kubectl apply -f ${POD_NAME}'''
	}
}
stage('checkout pods and service'){
	agent{
		label 'kuber'}
		steps{
			sh '''
			kubectl get pods -o wide
			kubectl get svc
			kubectl get deployments
			kubectl describe svc ${SERVICE_NAME}'''
		}
}
post {
        success {
            mail to: 'famidhashamshath@gmail.com',
                 subject: "SUCCESS: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'",
                 body: "Build succeeded: ${env.BUILD_URL}"
        }

        failure {
            mail to: 'famidhashamshath@gmail.com',
                 subject: "FAILED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'",
                 body: "Build failed: ${env.BUILD_URL}"
        }
    } 
}
