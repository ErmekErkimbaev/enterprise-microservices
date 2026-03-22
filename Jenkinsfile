pipeline {
    agent any

    environment {
        AWS_REGION = "us-east-2"
        ECR_REPO = "546941058014.dkr.ecr.us-east-2.amazonaws.com/auth-service"
    }

    stages {

        stage('Build Docker') {
            steps {
                dir('auth-service') {
                    sh 'docker build -t auth-service .'
                }
            }
        }

        stage('Login to ECR') {
            steps {
                sh '''
                aws ecr get-login-password --region $AWS_REGION | \
                docker login --username AWS --password-stdin 546941058014.dkr.ecr.us-east-2.amazonaws.com
                '''
            }
        }

        stage('Push Image') {
            steps {
                sh 'docker tag auth-service:latest $ECR_REPO:latest'
                sh 'docker push $ECR_REPO:latest'
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                kubectl apply -f auth-service/deployment.yaml
                kubectl apply -f auth-service/service.yaml
                '''
            }
        }
    }
}
