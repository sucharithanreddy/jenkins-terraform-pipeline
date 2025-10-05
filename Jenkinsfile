pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                checkout scm
                sh 'docker pull hashicorp/terraform:light'
            }
        }

        stage('Verify Docker') {
            steps {
                sh 'which docker'
                sh 'docker ps || true'
            }
        }

        stage('Terraform Init') {
            steps {
                sh '''
                docker run --rm -v /home/suchi/jenkins-terraform-pipeline:/workspace -w /workspace hashicorp/terraform:light init
                '''
            }
        }

        stage('Terraform Plan') {
            steps {
                sh '''
                docker run --rm -v /home/suchi/jenkins-terraform-pipeline:/workspace -w /workspace hashicorp/terraform:light plan
                '''
            }
        }

        stage('Terraform Apply') {
            steps {
                sh '''
                docker run --rm -v /home/suchi/jenkins-terraform-pipeline:/workspace -w /workspace hashicorp/terraform:light apply -auto-approve
                '''
            }
        }

        stage('Slack Notification') {
            steps {
                slackSend(channel: '#devops', message: "Terraform apply completed successfully!")
            }
        }
    }
}