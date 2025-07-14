pipeline {
  agent any

  parameters {
    string(name: 'INSTANCE_ID', defaultValue: 'i-07e528bbf536acdcd', description: 'EC2 instance ID')
    string(name: 'AWS_REGION', defaultValue: 'us-east-1', description: 'AWS region')
    string(name: 'start', defaultValue: 'false', description: 'Start EC2 instance? Set to true to start, false to stop.')
  }

  environment {
    AWS_CREDENTIALS_ID = 'jenkins-aws-start-stop'
  }

  stages {
    stage('Stop EC2 Instance') {
      steps {
        withCredentials([[
          $class: 'AmazonWebServicesCredentialsBinding',
          credentialsId: env.AWS_CREDENTIALS_ID
        ]]) {
          sh '''#!/bin/bash
            echo "Stopping EC2 instance $INSTANCE_ID in $AWS_REGION"

            aws ec2 stop-instances \
              --instance-ids "$INSTANCE_ID" \
              --region "$AWS_REGION"

            echo "Waiting for instance to stop..."
            aws ec2 wait instance-stopped \
              --instance-ids "$INSTANCE_ID" \
              --region "$AWS_REGION"

            echo "✅ EC2 instance $INSTANCE_ID stopped successfully."
          '''
        }
      }
    }
  }

  post {
    success {
      echo "✅ Pipeline completed: EC2 stopped."
    }
    failure {
      echo "❌ Pipeline failed: check credentials, CLI install, or parameters."
    }
  }
}