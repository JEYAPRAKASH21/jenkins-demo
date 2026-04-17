pipeline {
    agent any

    environment {
        IMAGE_NAME = "myapp"
        CONTAINER_NAME = "myapp-container"
    }

    stages {

        stage('Print Branch') {
            steps {
                echo "🚀 Building branch: ${env.BRANCH_NAME}"
            }
        }

        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    docker.build("${IMAGE_NAME}")
                }
            }
        }

        stage('Run Container') {
            steps {
                sh '''
                docker stop ${CONTAINER_NAME} || true
                docker rm ${CONTAINER_NAME} || true
                docker run -d -p 80:80 --name ${CONTAINER_NAME} ${IMAGE_NAME}
                '''
            }
        }
    }
}
