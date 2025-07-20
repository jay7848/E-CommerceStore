pipeline {
  agent any

  environment {
    AWS_REGION = 'us-west-2'
    CLUSTER_NAME = 'ecommerce-cluster'
  }

  stages {
    stage('Checkout') {
      steps {
        git url: 'https://github.com/jay7848/E-CommerceStore.git', branch: 'main'
      }
    }

    stage('Build Docker Images') {
      steps {
        script {
          def services = ['user', 'product', 'order', 'payment', 'frontend']
          for (service in services) {
            dir(service) {
              sh "docker build -t jay15229/${service}:latest ."
            }
          }
        }
      }
    }

    stage('Push to DockerHub') {
      steps {
        withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
          script {
            sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
            def services = ['user', 'product', 'order', 'payment', 'frontend']
            for (service in services) {
              sh "docker push jay15229/${service}:latest"
            }
          }
        }
      }
    }

    stage('Configure kubectl') {
      steps {
        withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', credentialsId: 'aws-creds']]) {
          sh '''
            aws configure set aws_access_key_id $AWS_ACCESS_KEY_ID
            aws configure set aws_secret_access_key $AWS_SECRET_ACCESS_KEY
            aws eks --region us-west-2 update-kubeconfig --name ecommerce-cluster
          '''
        }
      }
    }

    stage('Deploy to EKS') {
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
      echo '✅ Deployment completed successfully.'
    }
  }
}
