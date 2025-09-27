pipeline {
    agent { label 'docker-host' }  // Runs on Docker server node

    environment {
        IMAGE_NAME = "sidveenfin/jenkin-pipeline"  // Docker image nameo
	DOCKER_SERVER_IP = "172.31.39.22"  // Replace with your Docker server IP
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/siddharthpgreenyourbills/jenkins-pipeline'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $IMAGE_NAME:latest .'
            }
        }

        stage('Login to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: '8e2578da-0e7a-4e09-8d30-de8762ec2315', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
                }
            }
        }

        stage('Push Image') {
            steps {
                sh 'docker push $IMAGE_NAME:latest'
            }
        }

        stage('Deploy') {
            steps {
                withCredentials([file(credentialsId: 'docker-server-key', variable: 'SSH_KEY')]) {
                    sh """
                        chmod 600 $SSH_KEY
                        ssh -i $SSH_KEY -o StrictHostKeyChecking=no ${DEPLOY_USER}@${DOCKER_SERVER_IP} \\
                        "docker pull ${IMAGE_NAME}:latest && \\
                        docker compose -f ${DOCKER_COMPOSE_PATH} up -d --force-recreate"
                    """
                }
            }
        }
    }
}

