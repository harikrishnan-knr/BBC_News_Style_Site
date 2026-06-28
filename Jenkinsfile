pipeline{
    agent{
		label 'slave'
	}
	environment{
	IMAGE_NAME = 'bbcsite'
	CONTAINER_NAME = 'newsapp'	
                    }

	stages{
	stage('checkout'){
	steps{
	git branch: 'main', url: 'https://github.com/famidha2004/Nodejs-01.git'

                    }
    }
	stage('build'){
	steps{
	sh 'docker build -t ${IMAGE_NAME}:latest .'
                    }
    }
	stage('stop old containers'){
	steps{
	sh 'docker stop ${CONTAINER_NAME} || true'
	sh 'docker rm ${CONTAINER_NAME} || true'
	}
}
	stage('docker image run'){
	steps{
	sh 'docker run -d --name ${CONTAINER_NAME} -p 80:80 ${IMAGE_NAME}:latest'
	sh 'docker ps'
	}
}
}
}
