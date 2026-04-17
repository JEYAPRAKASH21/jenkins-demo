pipeline {
    agent any

    // 🔔 Trigger from GitHub webhook
    triggers {
        githubPush()
    }

    environment {
        IMAGE_NAME = "myapp"
        CONTAINER_NAME = "myapp-container"
        REPO_URL = "https://github.com/JEYAPRAKASH21/jenkins-demo.git"
        BRANCH_NAME = "main"   // default fallback
    }

    stages {

        // 🔍 Detect Branch (IMPORTANT FIX)
        stage('Detect Branch') {
            steps {
                script {
                    def branch = sh(
                        script: "git branch -r --contains HEAD | grep origin | head -n 1 | sed 's/origin\\///'",
                        returnStdout: true
                    ).trim()

                    if (!branch) {
                        branch = "main"
                    }

                    env.BRANCH_NAME = branch

                    echo "═══════════════════════════════════════"
                    echo "🚀 Detected Branch: ${env.BRANCH_NAME}"
                    echo "═══════════════════════════════════════"
                }
            }
        }

        // 📥 Checkout Code
        stage('Checkout') {
            steps {
                echo "🌿 Branch : ${env.BRANCH_NAME}"
                echo "📌 Stage : Checkout"

                git branch: "${env.BRANCH_NAME}",
                    url: "${env.REPO_URL}"
            }
        }

        // 🔨 Build (optional placeholder)
        stage('Build') {
            steps {
                echo "🌿 Branch : ${env.BRANCH_NAME}"
                echo "🔨 Stage : Build"

                sh '''
                echo "Building on branch: $BRANCH_NAME"
                '''
            }
        }

        // 🧪 Test (optional placeholder)
        stage('Test') {
            steps {
                echo "🌿 Branch : ${env.BRANCH_NAME}"
                echo "🧪 Stage : Test"

                sh '''
                echo "Running tests on branch: $BRANCH_NAME"
                '''
            }
        }

        // 🐳 Build Docker Image
        stage('Docker Build') {
            steps {
                script {
                    echo "🐳 Building Docker image for ${env.BRANCH_NAME}"
                    docker.build("${IMAGE_NAME}:${env.BRANCH_NAME}")
                }
            }
        }

        // 🚀 Deploy (Branch-based)
        stage('Deploy') {
            steps {
                script {

                    echo "🌿 Branch : ${env.BRANCH_NAME}"
                    echo "🚀 Stage : Deploy"

                    if (env.BRANCH_NAME == 'main' || env.BRANCH_NAME == 'master') {

                        echo "📦 Deploying to PRODUCTION"

                        sh """
                        docker stop ${CONTAINER_NAME} || true
                        docker rm ${CONTAINER_NAME} || true
                        docker run -d -p 80:80 --name ${CONTAINER_NAME} ${IMAGE_NAME}:${env.BRANCH_NAME}
                        """

                    } else if (env.BRANCH_NAME == 'dev') {

                        echo "📦 Deploying to DEV"

                        sh """
                        docker stop ${CONTAINER_NAME} || true
                        docker rm ${CONTAINER_NAME} || true
                        docker run -d -p 8081:80 --name ${CONTAINER_NAME} ${IMAGE_NAME}:${env.BRANCH_NAME}
                        """

                    } else if (env.BRANCH_NAME.startsWith('feature/')) {

                        echo "📦 Deploying FEATURE branch"

                        sh """
                        docker stop ${CONTAINER_NAME} || true
                        docker rm ${CONTAINER_NAME} || true
                        docker run -d -p 8082:80 --name ${CONTAINER_NAME} ${IMAGE_NAME}:${env.BRANCH_NAME}
                        """

                    } else {
                        echo "⚠️ No deployment rule for branch: ${env.BRANCH_NAME}"
                    }
                }
            }
        }

        // 🔔 Notify
        stage('Notify') {
            steps {
                echo "🔔 Pipeline complete for branch: ${env.BRANCH_NAME}"
            }
        }
    }

    // 📦 Post actions
    post {
        success {
            echo "✅ SUCCESS: ${env.BRANCH_NAME}"
        }
        failure {
            echo "❌ FAILURE: ${env.BRANCH_NAME}"
        }
        always {
            echo "🏁 Finished: ${env.BRANCH_NAME}"
            cleanWs()
        }
    }
}
