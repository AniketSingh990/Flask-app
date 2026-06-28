pipeline {

    agent any

    environment {
        IMAGE_NAME = "aniket967/flask-app"
        IMAGE_TAG = "${BUILD_NUMBER}"
    }

    stages {

        stage('Checkout Application Code') {
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
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {

                    sh '''
                    echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin

                    docker push ${IMAGE_NAME}:${IMAGE_TAG}
                    '''
                }
            }
        }

        stage('Clone K8s Manifest Repository') {
            steps {
                dir('k8s') {
                    git branch: 'main',
                        credentialsId: 'github',
                        url: 'https://github.com/AniketSingh990/python-k8s-manifests.git'
                }
            }
        }

        stage('Update deployment.yaml') {
            steps {
                dir('k8s') {

                    sh """
                    sed -i 's#image:.*#image: ${IMAGE_NAME}:${IMAGE_TAG}#' deployment.yaml

                    git config user.email "jenkins@example.com"
                    git config user.name "Jenkins"

                    git add deployment.yaml

                    git commit -m "Updated image to ${IMAGE_TAG}" || true
                    """

                }
            }
        }

        stage('Push Changes to GitHub') {
            steps {

                dir('k8s') {

                    withCredentials([usernamePassword(
                        credentialsId: 'github',
                        usernameVariable: 'GIT_USER',
                        passwordVariable: 'GIT_TOKEN'
                    )]) {

                        sh '''
                        git remote set-url origin https://${GIT_USER}:${GIT_TOKEN}@github.com/AniketSingh990/python-k8s-manifests.git

                        git push origin main
                        '''
                    }

                }

            }
        }

    }

    post {

        success {
            echo "========================================"
            echo "GitOps Pipeline Completed Successfully"
            echo "Docker Image : ${IMAGE_NAME}:${IMAGE_TAG}"
            echo "deployment.yaml Updated"
            echo "Changes Pushed to GitHub"
            echo "========================================"
        }

        failure {
            echo "========================================"
            echo "GitOps Pipeline Failed"
            echo "========================================"
        }

    }

}
