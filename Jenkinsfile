pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = credentials('8e2578da-0e7a-4e09-8d30-de8762ec2315')  // ID you will create in Jenkins
        IMAGE_NAME = "sidveenfin/jenkin-pipeline"  // replace with your Docker Hub repo
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/siddharthpgreenyourbills/jenkins-pipeline'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh 'docker build -t $IMAGE_NAME:latest .'
                }
            }
        }

        stage('Login to Docker Hub') {
            steps {
                script {
                    sh "echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin"
                }
            }
        }

        stage('Push Image') {
            steps {
                script {
                    sh 'docker push $IMAGE_NAME:latest'
                }
            }
        }

        stage('Deploy') {
            steps {
                // On your Docker server: pull new image and restart container
                sshagent (credentials: ['docker-server-ssh']) {
                    sh '''
                    ssh -o StrictHostKeyChecking=no ec2-user@<DOCKER_SERVER_IP> '
                        docker pull $IMAGE_NAME:latest &&
                        docker compose -f /path/to/docker-compose.yml up -d --force-recreate
                    '
                    '''
                }
            }
        }
    }
}

