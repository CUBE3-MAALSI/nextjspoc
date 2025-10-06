pipeline {
    agent any

    environment {
        DOCKER_CREDENTIALS_ID = 'dockerhub-credentials-id'
        DOCKER_IMAGE_NAME = 'luascale/nextjs-app'
        CONTAINER_NAME = 'nextjs-app'
        SERVER_PORT = '3000'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    dockerImage = docker.build(DOCKER_IMAGE_NAME)
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', DOCKER_CREDENTIALS_ID) {
                        dockerImage.push('latest')
                    }
                }
            }
        }

        stage('Deploy on Server') {
            steps {
                script {
                    // Ensure the container is stopped and removed if exists
                    sh """
                    docker stop ${CONTAINER_NAME} || true
                    docker rm ${CONTAINER_NAME} || true
                    docker pull ${DOCKER_IMAGE_NAME}:latest
                    docker run -d -p ${SERVER_PORT}:3000 --name ${CONTAINER_NAME} ${DOCKER_IMAGE_NAME}:latest
                    """
                }
            }
        }
    }

    post {
        always {
            cleanWs()
        }
    }
}
