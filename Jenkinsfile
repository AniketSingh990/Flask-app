pipeline {

    agent any

    environment {
        IMAGE_NAME = "aniket967/flask-app"
        IMAGE_TAG = "${BUILD_NUMBER}"
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Code Scan') {
            steps {
                sh '''
                bandit -r .
                '''
            }
        }

        stage('Build Docker Image') {
            steps {
                sh '''
                docker build -t ${IMAGE_NAME}:${IMAGE_TAG} .
                '''
            }
        }

        stage('Push Docker Image') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub',
                    usernameVariable: 'USER',
                    passwordVariable: 'PASS'
                )]) {

                    sh '''
                    echo "$PASS" | docker login -u "$USER" --password-stdin

                    docker push ${IMAGE_NAME}:${IMAGE_TAG}
                    '''
                }
            }
        }

        stage('Update K8s Repo') {
            steps {

                dir('k8s') {

                    git branch: 'main',
                        credentialsId: 'github',
                        url: 'https://github.com/AniketSingh990/python-k8s-manifests.git'

                    sh """
                    sed -i 's#image:.*#image: ${IMAGE_NAME}:${IMAGE_TAG}#' deployment.yaml

                    git config user.email "jenkins@example.com"
                    git config user.name "Jenkins"

                    git add deployment.yaml

                    git commit -m "Updated image to ${IMAGE_TAG}" || true

                    git push origin main
                    """
                }
            }
        }
    }

    post {
        success {
            echo "Pipeline Completed Successfully"
        }

        failure {
            echo "Pipeline Failed"
        }
    }
}
