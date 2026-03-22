pipeline {
    agent {
        kubernetes {
            inheritFrom 'default'
        }
    }

    stages {

        stage('Check AWS') {
            steps {
                sh 'aws --version'
            }
        }

        stage('Check kubectl') {
            steps {
                sh 'kubectl version --client'
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
