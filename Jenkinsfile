pipeline {
  agent any

  environment {
    AWS_REGION   = "us-east-1"
    ECR_REGISTRY = "861273430313.dkr.ecr.us-east-1.amazonaws.com"
    ECR_REPO     = "springboot-legacy-app"
    IMAGE_TAG    = "${env.BUILD_NUMBER}"
  }

  stages {
    stage("Checkout") {
      steps { checkout scm }
    }

    stage("Build (Maven)") {
      steps {
        sh 'mvn -B clean package -DskipTests'
        sh 'ls -lh target/*.jar'
      }
    }

    stage("Docker build") {
      steps {
        sh '''
          set -e
          docker build -t ${ECR_REPO}:${IMAGE_TAG} .
          docker tag ${ECR_REPO}:${IMAGE_TAG} ${ECR_REGISTRY}/${ECR_REPO}:${IMAGE_TAG}
        '''
      }
    }

    stage("Login to ECR") {
      steps {
        sh '''
          set -e
          aws ecr get-login-password --region ${AWS_REGION} \
            | docker login --username AWS --password-stdin ${ECR_REGISTRY}
        '''
      }
    }

    stage("Push to ECR") {
      steps {
        sh '''
          set -e
          docker push ${ECR_REGISTRY}/${ECR_REPO}:${IMAGE_TAG}
        '''
      }
    }
  }
}

