pipeline {
    agent any

    environment {
        IMAGE_NAME = "finance-website:latest"
    }

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/LevinRobert/finance-website.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t finance-website:latest .'
            }
        }

        stage('Run test') {
            steps {
                sh 'docker run -d finance-website'
            }
        }

        stage('Login to DockerHub') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-creds',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
                }
            }
        }

        stage('Push Image') {
            steps {
                sh 'docker push levin16robert/finance-website'
            }
        }
    }
}
