pipeline {
    agent any

    environment{
		    DOCKER_IMAGE_OWNER = 'docdker'
		    DOCKER_IMAGE_TAG = 'latest'
		    DOCKER_TOKEN = credentials('dockerhub')
    }
    stages {
        stage('clone from SCM') {
            steps {
                sh '''
                rm -rf msa-parking-service
                git clone https://github.com/evollivia/msa-parking-service.git
                '''
            }
        }
        stage('Docer Image Builging') {
            steps {
                sh '''
				cd msa-parking-service
                docker build -t ${DOCKER_IMAGE_OWNER}/msa-parking-service:${DOCKER_IMAGE_TAG} ./msa-parking-service
                '''
            }
        }
        stage('Docer Login') {
            steps {
                sh '''
                echo ${DOCKER_TOKEN_PSW} | docker login -u ${DOCKER_TOKEN_USR} --password-stdin
                '''
            }
        }
        stage('Docer Image Pushing') {
            steps {
                sh '''
                docker push ${DOCKER_IMAGE_OWNER}/msa-parking-service:${DOCKER_IMAGE_TAG}
                '''
            }
        }
        stage('Docer Logout') {
            steps {
                sh '''
                docker logout
                '''
            }
        }
    }
}