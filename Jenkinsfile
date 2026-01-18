pipeline {
    agent any

    environment {
        IMAGE_NAME = "levinrobert/finance-website:latest"
        TARGET_VM = "ec2-user@your-vm-ip"  // replace with your VM username and IP
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
                    credentialsId: 'target-vm-ssh',  // Jenkins SSH key credential for VM
                    keyFileVariable: 'SSH_KEY',
                    usernameVariable: 'SSH_USER'
                )]) {
                    sh """
                        ssh -i $SSH_KEY -o StrictHostKeyChecking=no $SSH_USER@$TARGET_VM '
                            docker login -u $DOCKER_USER -p $DOCKER_PASS &&
                            docker pull $IMAGE_NAME &&
                            docker stop finance-website || true &&
                            docker rm finance-website || true &&
                            docker run -d --name finance-website -p 3000:3000 $IMAGE_NAME
                        '
                    """
                }
            }
        }
    }
}
