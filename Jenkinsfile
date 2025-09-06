# Declarative Pipeline syntax
pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "bbamba/portfolio"
        DOCKER_TAG = "latest"
        REGISTRY_CREDENTIALS = "dockerhub-credentials"
        KUBE_CONFIG = "$HOME/.kube/config"
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
                    sh "docker build -t ${DOCKER_IMAGE}:${DOCKER_TAG} ."
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                withCredentials([usernamePassword(credentialsId: "${REGISTRY_CREDENDIALS}",usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    script {
                        sh """
                            echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                            docker push ${DOCKER_IMAGE}:${DOCKER_TAG}
                            docker logout
                           """
                    }
                }
            }
        }

        /*stage('Deploy to Kubernetes') {
            steps {
                script {
                    // apply manifests (deployment + service)
                    sh "kubectl apply -f k8s/deployment.yml"
                }
            }
        }*/
    }
    post {
        success {
            ech "🚀 Website deployed successfully on Kubernetes!"
        }
        failure {
            echo "❌ Deployment failed!"
        }
    }
}