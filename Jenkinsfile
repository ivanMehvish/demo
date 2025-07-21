pipeline {
  agent any
  parameters {
    string(name: 'STACK_NAME', defaultValue: 'mypipeline-infra')
    string(name: 'EC2_KEY', defaultValue: '')
  }
  environment {
    AWS_REGION = 'ap-south-1'
  }
  stages {
    stage('Validate Template') {
      steps {
        sh "aws cloudformation validate-template --template-body file://infra.yaml"
      }
    }
    stage('Cleanup ChangeSets') {
      steps {
        sh """
          for cs in \$(aws cloudformation list-change-sets --stack-name ${params.STACK_NAME} \
            --query 'Summaries[?Status==\`FAILED\`].ChangeSetName' \
            --output text); do
            aws cloudformation delete-change-set \
              --stack-name ${params.STACK_NAME} --change-set-name "\$cs"
          done
        """
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
            --capabilities CAPABILITY_NAMED_IAM \
            --no-fail-on-empty-changeset
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
