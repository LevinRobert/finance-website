pipeline {
    agent any

    environment {
        IMAGE_NAME = "levin16robert/finance-website:latest"
        TARGET_VM = "ec2-user@15.206.187.198"
    }

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/LevinRobert/finance-website.git'
            }
        }

        stage('DockerHub Login') {
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

        stage('Build Image') {
            steps {
                sh 'docker build -t $IMAGE_NAME .'
            }
        }

        stage('Push Image') {
            steps {
                sh 'docker push $IMAGE_NAME'
            }
        }

        stage('Deploy on Target VM') {
            steps {
                withCredentials([sshUserPrivateKey(
                    credentialsId: 'vm-ssh-key',
                    keyFileVariable: 'SSH_KEY',
                    usernameVariable: 'SSH_USER'
                )]) {
                    sh '''
                      ssh -i $SSH_KEY -o StrictHostKeyChecking=no $TARGET_VM "
                        docker pull ${IMAGE_NAME} &&
                        docker stop finance || true &&
                        docker rm finance || true &&
                        docker run -d --name finance -p 3000:6111 ${IMAGE_NAME}
                      "
                    '''
                }
            }
        }
    }
}
