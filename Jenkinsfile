pipeline {
  agent any
  environment {
    TERRAFORM_IMAGE = 'hashicorp/terraform:light'
    TERRAFORM_DIR = '/app'
  }
  stages {
    stage('Checkout') {
      steps {
        checkout scm
        sh "docker pull ${TERRAFORM_IMAGE}"
      }
    }

    stage('Verify Docker') {
      steps {
        sh 'which docker'
        sh 'docker ps || true'
      }
    }

    stage('Terraform') {
      steps {
        // Run all Terraform commands in a single container
        sh """
        docker run -w ${TERRAFORM_DIR} -v /root/.aws:/root/.aws -v $WORKSPACE:${TERRAFORM_DIR} ${TERRAFORM_IMAGE} \
        sh -c "
          terraform init &&
          terraform plan &&
          terraform apply -auto-approve
        "
        """
      }
    }

    stage('Slack Notification') {
      steps {
        slackSend(channel: '#devops', message: "Terraform apply completed successfully!")
      }
    }
  }
}