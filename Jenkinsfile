pipeline {
    agent any

    environment {
        DOCKER_HOST = 'tcp://localhost:2375'  // Add your Docker host here
    }

    stages {
        stage('Checkout') {
            steps {
                // Checkout main branch
                git branch: 'main', url: 'https://github.com/AniketSingh990/Flask-app.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                // Build Docker image named flask-app
                sh 'docker build -t flask-app .'
            }
        }

        stage('Run Docker Container') {
            steps {
                // Stop previous container if running
                sh 'docker rm -f flask-app || true'
                // Run container in detached mode on port 5000
                sh 'docker run -d -p 5000:5000 --name flask-app flask-app'
            }
        }
    }

    post {
        success {
            echo 'Deployment Successful!'
        }
        failure {
            echo 'Deployment Failed!'
        }
    }
}
