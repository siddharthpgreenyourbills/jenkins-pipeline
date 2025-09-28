pipeline {
  agent { label 'docker-host' }

  environment {
    IMAGE_NAME = "sidveenfin/jenkin-pipeline"
    DOCKER_COMPOSE_PATH = "/home/ec2-user/jenkins-pipeline/docker-compose.yml"
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
        withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
          sh 'echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin'
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
        // runs on docker-host node — so run compose locally
        sh "docker pull $IMAGE_NAME:latest"
        sh "docker compose -f $DOCKER_COMPOSE_PATH up -d --force-recreate"
      }
    }
  }
}

