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
                    // Jenkins automatically sets this if webhook is used
                    def branch = env.GIT_BRANCH

                    // fallback if null
                    if (!branch) {
                        branch = "origin/main"
                    }

                    // clean branch name
                    branch = branch.replace("origin/", "")

                    echo "🚀 Building branch: ${branch}"

                    // Checkout correct branch
                    checkout([
                        $class: 'GitSCM',
                        branches: [[name: "*/${branch}"]],
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
                    docker.build("${IMAGE_NAME}:${branch}")
                }
            }
        }

        stage('Run Container') {
            steps {
                sh """
                docker stop ${CONTAINER_NAME} || true
                docker rm ${CONTAINER_NAME} || true
                docker run -d -p 80:80 --name ${CONTAINER_NAME} ${IMAGE_NAME}:${branch}
                """
            }
        }
    }
}
