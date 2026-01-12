pipeline {
    agent any

    environment {
        IMAGE_NAME = "finance-website:latest"
    }

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/LevinRobert/finance-website.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t finance-website:latest .'
            }
        }

        stage('Run Tests') {
            steps {
                sh 'docker run -d finance-website'
              
            }
        }

    }

    

