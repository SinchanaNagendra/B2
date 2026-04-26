pipeline {
    agent any

    environment {
        DOCKERHUB_USER = 'sinchananagendra03'
        APP_NAME = 'myapp'
        IMAGE_TAG = "v${env.BUILD_NUMBER}"
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    echo "Building image: ${DOCKERHUB_USER}/${APP_NAME}:${IMAGE_TAG}"
                    bat "docker build -t ${DOCKERHUB_USER}/${APP_NAME}:${IMAGE_TAG} ."
                }
            }
        }

        stage('Login to Docker Hub') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-creds',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    script {
                        // Windows-safe login (avoid --password-stdin issues)
                        bat "docker login -u %DOCKER_USER% -p %DOCKER_PASS%"
                    }
                }
            }
        }

        stage('Push Image to Docker Hub') {
            steps {
                script {
                    bat "docker push %DOCKERHUB_USER%/%APP_NAME%:%IMAGE_TAG%"
                    bat "docker tag %DOCKERHUB_USER%/%APP_NAME%:%IMAGE_TAG% %DOCKERHUB_USER%/%APP_NAME%:latest"
                    bat "docker push %DOCKERHUB_USER%/%APP_NAME%:latest"
                }
            }
        }
    }

    post {
        always {
            echo "Cleaning up local images..."
            bat "docker rmi %DOCKERHUB_USER%/%APP_NAME%:%IMAGE_TAG% || exit 0"
        }
    }
}