pipeline {
    agent any

    environment {
        IMAGE_NAME = "myapp"
        CONTAINER_NAME = "myapp-container"
        REPO_URL = "https://github.com/JEYAPRAKASH21/jenkins-demo.git"
    }

    stages {

        stage('Detect Branch') {
            steps {
                script {
                    // Get actual checked-out branch
                    def branch = sh(
                        script: "git branch --show-current",
                        returnStdout: true
                    ).trim()

                    // fallback if detached HEAD
                    if (!branch) {
                        branch = sh(
                            script: "git name-rev --name-only HEAD | sed 's~remotes/origin/~~'",
                            returnStdout: true
                        ).trim()
                    }

                    env.BRANCH_NAME = branch

                    echo "🚀 Detected Branch: ${env.BRANCH_NAME}"
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh "docker build -t ${IMAGE_NAME}:${env.BRANCH_NAME} ."
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
