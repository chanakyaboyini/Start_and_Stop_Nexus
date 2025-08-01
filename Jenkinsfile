pipeline {
  agent any

  parameters {
    string(name: 'INSTANCE_ID', defaultValue: 'i-07e528bbf536acdcd', description: 'EC2 instance ID')
    string(name: 'AWS_REGION', defaultValue: 'us-east-1', description: 'AWS region')
    choice(name: 'ACTION', choices: ['start', 'stop'], description: 'Choose whether to start or stop the EC2 instance')
  }

  environment {
    AWS_CREDENTIALS_ID = 'jenkins-aws-start-stop'
  }

  stages {
    stage('Manage EC2 Instance') {
      steps {
        withCredentials([[
          $class: 'AmazonWebServicesCredentialsBinding',
          credentialsId: env.AWS_CREDENTIALS_ID
        ]]) {
          sh '''#!/bin/bash
            echo "Selected action: $ACTION"
            echo "Target EC2 instance: $INSTANCE_ID in region: $AWS_REGION"

            if [[ "$ACTION" == "start" ]]; then
              echo "Starting EC2 instance..."
              aws ec2 start-instances \
                --instance-ids "$INSTANCE_ID" \
                --region "$AWS_REGION"

              echo "Waiting for EC2 to enter running state..."
              aws ec2 wait instance-running \
                --instance-ids "$INSTANCE_ID" \
                --region "$AWS_REGION"

              echo "✅ EC2 instance $INSTANCE_ID is now running."

            elif [[ "$ACTION" == "stop" ]]; then
              echo "Stopping EC2 instance..."
              aws ec2 stop-instances \
                --instance-ids "$INSTANCE_ID" \
                --region "$AWS_REGION"

              echo "Waiting for EC2 to enter stopped state..."
              aws ec2 wait instance-stopped \
                --instance-ids "$INSTANCE_ID" \
                --region "$AWS_REGION"

              echo "✅ EC2 instance $INSTANCE_ID has been stopped."

            else
              echo "❌ Invalid ACTION: $ACTION. Use 'start' or 'stop'."
              exit 1
            fi
          '''
        }
      }
    }
  }

  post {
    success {
      echo "✅ Pipeline completed: EC2 $ACTION executed successfully."
    }
    failure {
      echo "❌ Pipeline failed during EC2 $ACTION. Check CLI, credentials, or parameters."
    }
  }
}