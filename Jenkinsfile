pipeline {
    agent any

    environment {
        IMAGE = "<your-dockerhub-username>/flask-k8s-demo"
        KUBECONFIG = credentials('kubeconfig-secret')
        DOCKERHUB = credentials('dockerhub-credentials')
    }

    stages {

        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    docker.build("${IMAGE}:${BUILD_NUMBER}")
                }
            }
        }

        stage('Login to Docker Hub') {
            steps {
                script {
                    sh "echo ${DOCKERHUB_PSW} | docker login -u ${DOCKERHUB_USR} --password-stdin"
                }
            }
        }

        stage('Push Image to Docker Hub') {
            steps {
                script {
                    sh "docker push ${IMAGE}:${BUILD_NUMBER}"
                    sh "docker tag ${IMAGE}:${BUILD_NUMBER} ${IMAGE}:latest"
                    sh "docker push ${IMAGE}:latest"
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                script {
                    // Update image version on-the-fly
                    sh """
                    kubectl --kubeconfig=${KUBECONFIG} set image deployment/flask-k8s-app \
                    flask-container=${IMAGE}:${BUILD_NUMBER}
                    """
                }
            }
        }
    }

    post {
        success { echo "🎉 Deployment to Kubernetes successful!" }
        failure { echo "❌ Deployment failed!" }
    }
}
