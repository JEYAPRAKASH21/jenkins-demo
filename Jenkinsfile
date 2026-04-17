pipeline {
    agent any

    environment {
        IMAGE_NAME = "myapp"
        CONTAINER_NAME = "myapp-container"
        BRANCH_NAME = ""
    }

    stages {

        stage('Detect Branch') {
            steps {
                script {

                    // Get branch from git
                    def branch = sh(
                        script: "git rev-parse --abbrev-ref HEAD",
                        returnStdout: true
                    ).trim()

                    // Fix if detached HEAD
                    if (branch == "HEAD") {
                        branch = sh(
                            script: "git name-rev --name-only HEAD",
                            returnStdout: true
                        ).trim().replace("remotes/origin/", "")
                    }

                    // Save globally
                    env.BRANCH_NAME = branch

                    echo "🚀 Building branch: ${env.BRANCH_NAME}"
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    docker.build("${IMAGE_NAME}:${env.BRANCH_NAME}")
                }
            }
        }

        stage('Run Container') {
            steps {
                sh """
                docker stop ${CONTAINER_NAME} || true
                docker rm ${CONTAINER_NAME} || true
                docker run -d -p 80:80 --name ${CONTAINER_NAME} ${IMAGE_NAME}:${env.BRANCH_NAME}
                """
            }
        }
    }
}
