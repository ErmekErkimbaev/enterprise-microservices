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
      image: bitnami/kubectl:latest
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
