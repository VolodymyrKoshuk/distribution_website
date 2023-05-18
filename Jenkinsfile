pipeline {
  agent {
    label 'awscli'
  }

  parameters {
    choice(name: 'environment', choices: ['dev', 'prod'], description: 'Select environment')
  }

  stages {
    stage('Checkout') {
      steps {
        script {
          sh "git checkout ${params.environment}"
        }
      }
    }

    stage('Deploy to S3') {
      steps {
        script {
          def bucketName

          if (params.environment == 'dev') {
            bucketName = "koshuk-bucket-dev"
          } else if (params.environment == 'prod') {
            bucketName = "koshuk-bucket-prod"
          }

          sh "aws s3 sync . s3://${bucketName} --delete"
        }
      }
    }

    stage('Invalidate CloudFront Cache') {
      steps {
        script {
          def distributionId = "E1KN5VEYEJLICQ"

          sh "aws cloudfront create-invalidation --distribution-id ${distributionId} --paths /*"
        }
      }
    }
  }
}