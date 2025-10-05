pipeline {
    agent any

    stages {
     stage('Checkout') {
            steps {
                checkout scm
                sh 'docker pull hashicorp/terraform:light'
            }
        }

        stage('Debug Workspace') {
            steps {
                echo "Listing workspace files..."
                sh 'ls -l $WORKSPACE'
                sh 'ls -l $WORKSPACE/*.tf'
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
                docker run --rm \
                  -v $WORKSPACE:/workspace \
                  -v /home/suchi/.aws:/root/.aws:ro \
                  -w /workspace \
                  hashicorp/terraform:light init
                '''
            }
        }
        stage('Terraform Plan') {
            steps {
                sh '''
                docker run --rm \
                  -v $WORKSPACE:/workspace \
                  -v /home/suchi/.aws:/root/.aws:ro \
                  -w /workspace \
                  hashicorp/terraform:light plan
                '''
            }
        }

        stage('Terraform Apply') {
            steps {
                sh '''
                docker run --rm \
                  -v $WORKSPACE:/workspace \
                  -v /home/suchi/.aws:/root/.aws:ro \
                  -w /workspace \
                  hashicorp/terraform:light apply -auto-approve
                '''
            }
        }
    }

    post {
        success {
            slackSend(channel: '#devops', message: "Terraform apply completed successfully!")
        }
        failure {
            slackSend(channel: '#devops', message: "Terraform pipeline failed. Check Jenkins for details.")
        }
    }
}
