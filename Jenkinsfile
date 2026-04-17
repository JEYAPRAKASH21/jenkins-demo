pipeline {
    agent any

    parameters {
        string(name: 'BRANCH_NAME', defaultValue: 'main', description: 'Git branch to build')
    }

    environment {
        IMAGE_NAME = "myapp"
        CONTAINER_NAME = "myapp-container"
    }

    stages {

        stage('Print Branch') {
            steps {
                echo "🚀 Building branch: ${params.BRANCH_NAME}"
            }
        }

        stage('Checkout Code') {
            steps {
                git branch: "${params.BRANCH_NAME}",
                url: 'https://github.com/JEYAPRAKASH21/jenkins-demo.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    docker.build("${IMAGE_NAME}:${params.BRANCH_NAME}")
                }
            }
        }

        stage('Run Container') {
            steps {
                sh """
                docker stop ${CONTAINER_NAME} || true
                docker rm ${CONTAINER_NAME} || true
                docker run -d -p 80:80 --name ${CONTAINER_NAME} ${IMAGE_NAME}:${params.BRANCH_NAME}
                """
            }
        }
    }
}
