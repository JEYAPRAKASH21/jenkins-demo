pipeline {
    agent any

    environment {
        IMAGE_NAME = "myapp"
        CONTAINER_NAME = "myapp-container"
        BRANCH_NAME = ""   // global variable
    }

    stages {

        stage('Checkout Code') {
            steps {
                script {
                    // Get branch from webhook
                    def branch = env.GIT_BRANCH

                    if (!branch) {
                        branch = "origin/main"
                    }

                    branch = branch.replace("origin/", "")

                    // save globally
                    env.BRANCH_NAME = branch

                    echo "🚀 Building branch: ${env.BRANCH_NAME}"

                    checkout([
                        $class: 'GitSCM',
                        branches: [[name: "*/${env.BRANCH_NAME}"]],
                        userRemoteConfigs: [[
                            url: 'https://github.com/JEYAPRAKASH21/jenkins-demo.git'
                        ]]
                    ])
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
