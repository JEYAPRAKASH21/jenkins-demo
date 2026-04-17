pipeline {
    agent any

    environment {
        IMAGE_NAME = "myapp"
        CONTAINER_NAME = "myapp-container"
    }

    stages {

        stage('Checkout Code') {
            steps {
                script {
                    // Extract branch name from Git
                    BRANCH = sh(
                        script: "git ls-remote --symref origin HEAD | grep refs/heads | awk '{print \$2}' | sed 's@refs/heads/@@'",
                        returnStdout: true
                    ).trim()

                    echo "🚀 Building branch: ${BRANCH}"

                    // Checkout actual branch
                    sh "git checkout ${BRANCH}"
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    docker.build("${IMAGE_NAME}:${BRANCH}")
                }
            }
        }

        stage('Run Container') {
            steps {
                sh """
                docker stop ${CONTAINER_NAME} || true
                docker rm ${CONTAINER_NAME} || true
                docker run -d -p 80:80 --name ${CONTAINER_NAME} ${IMAGE_NAME}:${BRANCH}
                """
            }
        }
    }
}
