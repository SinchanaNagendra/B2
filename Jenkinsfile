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
                    // Change 'sh' to 'bat' for Windows
                    bat "docker build -t ${DOCKERHUB_USER}/${APP_NAME}:${IMAGE_TAG} ."
                }
            }
        }

        stage('Login & Push to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', 
                                  passwordVariable: 'DOCKER_PASS', 
                                  usernameVariable: 'DOCKER_USER')]) {
                    script {
                        // Change 'sh' to 'bat' and update the login syntax for Windows cmd
                        bat "echo %DOCKER_PASS% | docker login -u %DOCKER_USER% --password-stdin"
                        bat "docker push %DOCKER_USER%/%APP_NAME%:%IMAGE_TAG%"
                        
                        bat "docker tag %DOCKER_USER%/%APP_NAME%:%IMAGE_TAG% %DOCKER_USER%/%APP_NAME%:latest"
                        bat "docker push %DOCKER_USER%/%APP_NAME%:latest"
                    }
                }
            }
        }
    }

    post {
        always {
            echo "Cleaning up local images..."
            // Use 'bat' and the Windows way to ignore errors (|| exit 0)
            bat "docker rmi %DOCKERHUB_USER%/%APP_NAME%:%IMAGE_TAG% || exit 0"
        }
    }
}