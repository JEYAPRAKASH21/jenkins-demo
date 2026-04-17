pipeline {
    agent any

    triggers {
        githubPush()
    }

    environment {
        IMAGE_NAME = "myapp"
        CONTAINER_NAME = "myapp-container"
        REPO_URL = "https://github.com/JEYAPRAKASH21/jenkins-demo.git"
        BRANCH_NAME = ""
    }

    stages {

        stage('Detect Branch') {
            steps {
                script {
                    // Extract branch from GitHub webhook
                    def branch = env.GIT_BRANCH

                    // fallback: extract from git
                    if (!branch) {
                        branch = sh(
                            script: "git log -1 --pretty=%D | sed 's/.*, origin\\///' | sed 's/,.*//'",
                            returnStdout: true
                        ).trim()
                    }

                    // clean
                    branch = branch.replace("origin/", "").trim()

                    env.BRANCH_NAME = branch

                    echo "🚀 Building branch: ${env.BRANCH_NAME}"
                }
            }
        }

        stage('Checkout Correct Branch') {
            steps {
                script {
                    checkout([
                        $class: 'GitSCM',
                        branches: [[name: "*/${env.BRANCH_NAME}"]],
                        userRemoteConfigs: [[
                            url: "${env.REPO_URL}"
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
