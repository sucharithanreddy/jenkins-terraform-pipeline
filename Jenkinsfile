pipeline {
    agent any
    stages {
        stage('Checkout') {
            steps {
                checkout scm
                sh 'docker pull hashicorp/terraform:light'
            }
        }

        stage('Terraform') {
            steps {
                sh '''
                docker run --rm -v $WORKSPACE:/workspace -w /workspace hashicorp/terraform:light init
                docker run --rm -v $WORKSPACE:/workspace -w /workspace hashicorp/terraform:light plan
                docker run --rm -v $WORKSPACE:/workspace -w /workspace hashicorp/terraform:light apply -auto-approve
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