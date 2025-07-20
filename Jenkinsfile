pipeline {
  agent any

  environment {
    AWS_REGION = 'us-west-2'
    CLUSTER_NAME = 'ecommerce-cluster'
    DOCKERHUB_CREDENTIALS_ID = 'dockerhub-credentials'  // Jenkins secret ID
    DOCKERHUB_USERNAME = 'jay15229'
  }

  stages {
    stage('Checkout Code') {
      steps {
        git url: 'https://github.com/jay7848/E-CommerceStore.git', branch: 'main'
      }
    }

    stage('Build and Push Docker Images') {
      steps {
        script {
          def services = ['user', 'product', 'order', 'payment', 'frontend']

          withCredentials([usernamePassword(
            credentialsId: "${DOCKERHUB_CREDENTIALS_ID}",
            usernameVariable: 'DOCKER_USERNAME',
            passwordVariable: 'DOCKER_PASSWORD'
          )]) {
            sh "echo $DOCKER_PASSWORD | docker login -u $DOCKER_USERNAME --password-stdin"

            for (service in services) {
              sh """
                docker build -t $DOCKERHUB_USERNAME/${service}-service ./\${service}
                docker push $DOCKERHUB_USERNAME/${service}-service
              """
            }
          }
        }
      }
    }

    stage('Update kubeconfig') {
      steps {
        withCredentials([[
          $class: 'AmazonWebServicesCredentialsBinding',
          credentialsId: 'aws-credentials'
        ]]) {
          sh """
            aws configure set aws_access_key_id $AWS_ACCESS_KEY_ID
            aws configure set aws_secret_access_key $AWS_SECRET_ACCESS_KEY
            aws eks --region $AWS_REGION update-kubeconfig --name $CLUSTER_NAME
          """
        }
      }
    }

    stage('Deploy to Kubernetes') {
      steps {
        sh "kubectl apply -f k8s/"
      }
    }
  }

  post {
    failure {
      echo "❌ Deployment failed. Check logs."
    }
    success {
      echo "✅ Deployment successful!"
    }
  }
}
