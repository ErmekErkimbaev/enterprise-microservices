pipeline {
    agent {
    kubernetes {
        label 'jenkins-agent'
        defaultContainer ''
    }
}
   

    environment {
        AWS_REGION = "us-east-2"
        ACCOUNT_ID = "546941058014"
        ECR_REPO = "${ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com/auth-service"
        IMAGE_TAG = "${env.GIT_COMMIT.take(7)}"
    }

    stages {
        stage('Build & Push') {
            steps {
                sh '''
                aws ecr get-login-password --region $AWS_REGION | \
                docker login --username AWS --password-stdin $ECR_REPO

                docker build -t auth-service ./auth-service
                docker tag auth-service:latest $ECR_REPO:$IMAGE_TAG
                docker push $ECR_REPO:$IMAGE_TAG
                '''
            }
        }
        stage('Build') {
            steps {
                container('docker') {
                    sh 'docker --version'
                }
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                kubectl set image deployment/auth-service \
                auth-service=$ECR_REPO:$IMAGE_TAG
                '''
            }
        }
    }
}
