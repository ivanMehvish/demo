pipeline {
  agent any
  parameters {
    string(name: 'STACK_NAME', defaultValue: 'mypipeline-infra', description: 'CloudFormation stack name')
    string(name: 'EC2_KEY', defaultValue: '', description: 'Existing EC2 KeyPair name')
  }
  environment {
    AWS_REGION = 'ap-south-1'  // adjust to your region
  }
  stages {
    stage('Validate Template') {
      steps {
        sh "aws cloudformation validate-template --template-body file://infra.yaml"
      }
    }
    stage('Deploy Stack') {
      steps {
        sh """
          aws cloudformation deploy \
            --stack-name ${params.STACK_NAME} \
            --template-file infra.yaml \
            --parameter-overrides EC2KeyName=${params.EC2_KEY} \
            --region ${env.AWS_REGION} \
            --capabilities CAPABILITY_NAMED_IAM
        """
      }
    }
    stage('Show Outputs') {
      steps {
        sh "aws cloudformation describe-stacks --stack-name ${params.STACK_NAME} --query 'Stacks[0].Outputs'"
      }
    }
  }
}
