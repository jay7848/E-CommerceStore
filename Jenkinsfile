pipeline {
  agent any

  environment {
    AWS_DEFAULT_REGION = 'us-west-2'
    CLUSTER_NAME = 'ecommerce-cluster'  // Replace if needed
  }

  stages {
    stage('Checkout') {
      steps {
        git 'https://github.com/jay7848/E-CommerceStore.git'
      }
    }

    stage('Set up AWS CLI & Kubeconfig') {
      steps {
        withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', credentialsId: 'your-aws-creds-id']]) {
          sh '''
            aws configure set aws_access_key_id $AWS_ACCESS_KEY_ID
            aws configure set aws_secret_access_key $AWS_SECRET_ACCESS_KEY
            aws eks --region $AWS_DEFAULT_REGION update-kubeconfig --name $CLUSTER_NAME
            kubectl get nodes
          '''
        }
      }
    }

    stage('Build Docker Images') {
      steps {
        sh '''
          docker build -t jay15229/product-service ./product-service
          docker build -t jay15229/order-service ./order-service
          docker build -t jay15229/user-service ./user-service
          docker build -t jay15229/ecommerce-frontend ./ecommerce-frontend
        '''
      }
    }

    stage('Push Docker Images') {
      steps {
        withCredentials([string(credentialsId: 'dockerhub-password', variable: 'DOCKER_HUB_PASSWORD')]) {
          sh '''
            echo "$DOCKER_HUB_PASSWORD" | docker login -u jay15229 --password-stdin
            docker push jay15229/product-service
            docker push jay15229/order-service
            docker push jay15229/user-service
            docker push jay15229/ecommerce-frontend
          '''
        }
      }
    }

    stage('Deploy to EKS') {
      steps {
        sh '''
          kubectl apply -f k8s/
        '''
      }
    }
  }
}
