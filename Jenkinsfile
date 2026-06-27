
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

    }

    post {

        success {

            echo "Docker Image Successfully Pushed"

        }

        failure {

            echo "Pipeline Failed"

        }

    }

}
