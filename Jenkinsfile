pipeline {
    agent any

    environment {
        IMAGE_NAME = "flask-webhook-demo"
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/BhavyachowdaryA/docker-webhook-demo.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    docker.build("${IMAGE_NAME}")
                }
            }
        }

        stage('Run Docker Container') {
            steps {
                script {
                    // Stop and remove existing container if running
                    sh "docker rm -f ${IMAGE_NAME} || true"
                    // Run new container
                    sh "docker run -d -p 5000:5000 --name ${IMAGE_NAME} ${IMAGE_NAME}"
                }
            }
        }
    }

    post {
        success {
            echo "Deployment Successful!"
        }
        failure {
            echo "Deployment Failed!"
        }
    }
}
