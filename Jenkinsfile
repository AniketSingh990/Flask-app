pipeline {
    agent any

    environment {
        DOCKER_HOST = 'tcp://localhost:2375'
    
        CONTAINER_NAME = 'flask-app'
        IMAGE_NAME = "flask-app:latest"
    }

    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/AniketSingh990/Flask-app.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh "docker build -t ${IMAGE_NAME} ."
                }
            }
        }

        stage('Run Docker Container') {
            steps {
                script {
                    // Stop existing container if running
                    sh "docker rm -f flask-app-container || true"
                    
                    // Run new container
                    sh "docker run -d --name flask-app-container -p 5000:5000 ${IMAGE_NAME}"
                }
            }
        }
    }

    post {
        success {
            echo "Application Deployed Successfully!"
        }
        failure {
            echo "Deployment Failed!"
        }
    }
}
