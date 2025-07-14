pipeline {
  agent any

  parameters {
    string(name: 'INSTANCE_ID', defaultValue: 'i-0123456789abcdef0', description: 'EC2 instance ID')
    string(name: 'AWS_REGION', defaultValue: 'us-east-1', description: 'AWS region')
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
          sh '''
            echo "Stopping EC2 instance ${params.INSTANCE_ID} in ${params.AWS_REGION}"
            aws ec2 stop-instances --instance-ids ${params.INSTANCE_ID} --region ${params.AWS_REGION}
            aws ec2 wait instance-stopped --instance-ids ${params.INSTANCE_ID} --region ${params.AWS_REGION}
            echo "✅ EC2 instance ${params.INSTANCE_ID} stopped successfully."
          '''
        }
      }
    }
  }
}