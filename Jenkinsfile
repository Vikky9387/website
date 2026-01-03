pipeline {
  agent any

  environment {
    DOCKER_IMAGE = "<DOCKER_USERNAME>/website"
  }

  stages {

    stage('Checkout') {
      steps {
        git branch: 'master',
            credentialsId: 'github-token',
            url: 'https://github.com/Vikky9387/website.git'
      }
    }

    stage('Build Image') {
      steps {
        sh 'docker build -t $DOCKER_IMAGE:latest .'
      }
    }

    stage('Push Image') {
      steps {
        withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
          sh '''
            echo $PASS | docker login -u $USER --password-stdin
            docker push $DOCKER_IMAGE:latest
          '''
        }
      }
    }

    stage('Deploy to Kubernetes') {
      steps {
        sh '''
          kubectl apply -f k8s/deployment.yml
          kubectl apply -f k8s/service.yml
        '''
      }
    }
  }
}
