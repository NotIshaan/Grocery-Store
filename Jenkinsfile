pipeline {
    agent any

    environment {
        DOCKER_HUB_CREDENTIALS = 'alaypatel' // Replace with your Jenkins credential ID
        DOCKER_IMAGE_NAME = 'grocery_store_image' // Updated Docker image name
        DOCKER_CONTAINER_NAME = 'alay2003/grocerystore' // Updated Docker container name
        IMAGE_TAG = 'new' // Specify the tag for the image
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                script {
                    // Install dependencies
                    bat 'npm install'
                }
            }
        }

        stage('Start Server') {
            steps {
                script {
                    // Start the server in the background
                    bat 'start cmd /c node server.js'
                    sleep 10 // wait for the server to start
                }
            }
        }

        stage('Test') {
            steps {
                script {
                    // Run the tests
                    bat 'node cart_test.js'
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    // Build the Docker image
                    bat "docker build -t ${DOCKER_IMAGE_NAME}:${IMAGE_TAG} ."
                }
            }
        }

        stage('Login to Docker') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: DOCKER_HUB_CREDENTIALS, passwordVariable: 'DOCKER_PASSWORD', usernameVariable: 'DOCKER_USERNAME')]) {
                        // Securely login to Docker using the credentials
                        bat "cmd /c echo ${DOCKER_PASSWORD} | docker login -u ${DOCKER_USERNAME} --password-stdin"
                    }
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    // Push the Docker image to Docker Hub
                    bat "docker push ${DOCKER_IMAGE_NAME}:${IMAGE_TAG}"
                }
            }
        }
    }

    post {
        always {
            script {
                // Clean up actions, if needed
                echo 'Cleaning up...'
                // You could stop the server or perform other cleanup actions here
            }
        }
    }
}
