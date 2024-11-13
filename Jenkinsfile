pipeline {
    agent any

    environment {
        DOCKER_IMAGE_OWNER = 'docdker'
        DOCKER_BUILD_TAG = "v${env.BUILD_NUMBER}"
        DOCKER_PWD = credentials('dockerhub')
        GIT_CREDENTIALS = credentials('github_token')  # 본인 credentials 이름
        REPO_URL = 'gongbu22/project-parking-CD.git'
        COMMIT_MESSAGE = 'Update README.md via Jenkins Pipeline'
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

        stage('Docker Image Building') {
            steps {
                dir('msa-parking-service'){
                sh '''
                docker build --platform linux/arm64 -t ${DOCKER_IMAGE_OWNER}/arm64-parking-service:latest -t ${DOCKER_IMAGE_OWNER}/arm64-parking-service:${DOCKER_BUILD_TAG} ./msa-parking-service
                docker tag ${DOCKER_IMAGE_OWNER}/arm64-parking-service:latest ${DOCKER_IMAGE_OWNER}/arm64-parking-service:${DOCKER_BUILD_TAG}
                '''
                }
            }
        }

        stage('Docker Login') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'DOCKER_USR', passwordVariable: 'DOCKER_PWD')]) {
                sh "echo $DOCKER_PWD | docker login -u $DOCKER_USR --password-stdin"}
            }
        }

        stage('Docker Image pushing') {
            steps {
                sh '''
                docker push ${DOCKER_IMAGE_OWNER}/arm64-parking-service:${DOCKER_BUILD_TAG}
                docker push ${DOCKER_IMAGE_OWNER}/arm64-parking-service:latest
                '''
            }
        }

        stage('Docker Logout') {
            steps {
                sh '''
                docker logout
                '''
            }
        }

        stage('Clone Repository') {
            steps {
                sh '''
                rm -rf project-parking-CD
                git clone https://github.com/${REPO_URL}
                '''
            }
        }

        stage('Modify README.md') {
            steps {
                sh """
                    cd project-parking-CD
                    echo "# Updated README" > README.md
                    echo "This README was updated by Jenkins Build #${env.BUILD_NUMBER} on \$(date)" >> README.md
                """
            }
        }

        stage('Commit Changes') {
            steps {
                dir('project-parking-CD') {
                sh '''
                    git config user.name "evollivia"
                    git config user.email "ytafxt@naver.com"
                    git add README.md
                    git commit -m "${COMMIT_MESSAGE}"
                '''
                }
            }
        }

        stage('Push Changes') {
            steps {
                dir('project-parking-CD') {
                sh '''
                    git push https://${GIT_CREDENTIALS_USR}:${GIT_CREDENTIALS_PSW}@github.com/${REPO_URL} main
                '''
                }
            }
        }
    }
}
