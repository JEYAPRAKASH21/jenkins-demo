pipeline {
    agent any

    environment {
        IMAGE_NAME = "myapp"
        CONTAINER_NAME = "myapp-container"
        BRANCH_NAME = "main"   // default fallback
    }

    stages {

        stage('Detect Branch') {
            steps {
                script {

                    // Try Jenkins env first
                    def branch = env.GIT_BRANCH

                    // If null → fallback using git
                    if (!branch) {
                        branch = sh(
                            script: "git branch -r --contains HEAD | grep origin | head -n 1 | sed 's/origin\\///'",
                            returnStdout: true
                        ).trim()
                    }

                    // cleanup
                    branch = branch.replace("origin/", "").trim()

                    // save globally
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
