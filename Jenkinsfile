pipeline {
    agent any

    environment {
        AWS_REGION = "us-east-2"
        ACCOUNT_ID = "546941058014"
        ECR_REPO = "${ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com/auth-service"
        IMAGE_TAG = "${env.GIT_COMMIT.take(7)}"
    }

    stages {

        stage('Checkout') {
            steps {
                git 'https://github.com/ErmekErkimbaev/enterprise-microservices.git'
            }
        }

        stage('Build & Push (Kaniko)') {
            steps {
                sh '''
                kubectl run kaniko \
                  --rm -i --tty \
                  --image=gcr.io/kaniko-project/executor:latest \
                  --restart=Never \
                  --overrides='
                  {
                    "spec": {
                      "containers": [{
                        "name": "kaniko",
                        "image": "gcr.io/kaniko-project/executor:latest",
                        "args": [
                          "--dockerfile=auth-service/Dockerfile",
                          "--context=dir:///workspace",
                          "--destination=''' + ECR_REPO + ''':' + IMAGE_TAG + '''"
                        ],
                        "volumeMounts": [{
                          "name": "workspace",
                          "mountPath": "/workspace"
                        }]
                      }],
                      "volumes": [{
                        "name": "workspace",
                        "emptyDir": {}
                      }]
                    }
                  }'
                '''
            }
        }

        stage('Update GitOps Repo') {
            steps {
                sh '''
                git clone https://github.com/YOUR_USERNAME/enterprise-gitops.git
                cd enterprise-gitops/dev

                sed -i "s|image: .*|image: ${ECR_REPO}:${IMAGE_TAG}|g" auth.yaml

                git config user.email "jenkins@example.com"
                git config user.name "jenkins"

                git add .
                git commit -m "update image ${IMAGE_TAG}"
                git push
                '''
            }
        }
    }
}
