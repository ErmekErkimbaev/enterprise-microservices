pipeline {
    agent {
        kubernetes {
            yaml """
apiVersion: v1
kind: Pod
spec:
  containers:
    - name: aws
      image: amazon/aws-cli:latest
      command:
        - cat
      tty: true

    - name: kubectl
      image: alpine/k8s:1.29.0
      command:
        - cat
      tty: true

    - name: docker
      image: docker:24.0.5
      command:
        - cat
      tty: true
"""
        }
    }

    stages {

        stage('Check AWS') {
            steps {
                container('aws') {
                    sh 'aws --version'
                }
            }
        }

        stage('Check kubectl') {
            steps {
                container('kubectl') {
                    sh 'kubectl version --client'
                }
            }
        }

        stage('Check Docker') {
            steps {
                container('docker') {
                    sh 'docker --version'
                }
            }
        }

    }
}
stage('Update GitOps Repo') {
    steps {
        container('aws') {
            withCredentials([usernamePassword(
                credentialsId: 'github-creds',
                usernameVariable: 'GIT_USER',
                passwordVariable: 'GIT_PASS'
            )]) {
                sh '''
                git clone https://$GIT_USER:$GIT_PASS@github.com/ErmekErkimbaev/enterprise-gitops.git gitops
                cd gitops/dev

                TAG=${GIT_COMMIT:0:7}

                sed -i "s|image: .*|image: 546941058014.dkr.ecr.us-east-2.amazonaws.com/auth-service:$TAG|g" auth.yaml

                git config user.email "jenkins@example.com"
                git config user.name "jenkins"

                git add .
                git commit -m "update image $TAG" || echo "no changes"
                git push
                '''
            }
        }
    }
}
