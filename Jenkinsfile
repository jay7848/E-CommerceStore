pipeline {
  agent any

  environment {
    DOCKERHUB_CREDENTIALS = credentials('dockerhub-creds')
    DOCKER_USERNAME = 'jay15229'
  }

  stages {
    stage('Checkout Code') {
      steps {
        git 'https://github.com/jay7848/E-CommerceStore.git'
      }
    }

    stage('Build and Push Docker Images') {
      steps {
        withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASSWORD')]) {
          sh 'echo $DOCKER_PASSWORD | docker login -u $DOCKER_USER --password-stdin'

          // Build and push each service
          dir('user-service') {
            sh 'docker build -t $DOCKER_USERNAME/user-service .'
            sh 'docker push $DOCKER_USERNAME/user-service'
          }

          dir('order-service') {
            sh 'docker build -t $DOCKER_USERNAME/order-service .'
            sh 'docker push $DOCKER_USERNAME/order-service'
          }

          dir('product-service') {
            sh 'docker build -t $DOCKER_USERNAME/product-service .'
            sh 'docker push $DOCKER_USERNAME/product-service'
          }

          dir('frontend') {
            sh 'docker build -t $DOCKER_USERNAME/frontend .'
            sh 'docker push $DOCKER_USERNAME/frontend'
          }
        }
      }
    }

    stage('Update kubeconfig') {
      steps {
        sh 'aws eks update-kubeconfig --region ap-south-1 --name <your-cluster-name>'
      }
    }

    stage('Deploy to Kubernetes') {
      steps {
        sh 'kubectl apply -f k8s/'
      }
    }
  }

  post {
    failure {
      echo '❌ Deployment failed. Check logs.'
    }
    success {
      echo '✅ Deployment succeeded.'
    }
  }
}
