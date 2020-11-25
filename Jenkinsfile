pipeline {
  agent any

  stages {
    stage('Build') {
      steps {
        sh 'sam build'
        stash includes: '**/.aws-sam/**/*', name: 'aws-sam'
      }
    }
    stage('beta') {
      environment {
        STACK_NAME = 'sam-app-beta-stage'
        S3_BUCKET = 'sam-jenkins-demo-us-west-2'
      }
      steps {
        withAWS(credentials: 'sam-jenkins-demo-credentials', region: 'us-west-2') {
          unstash 'aws-sam'
          sh 'sam deploy --stack-name $STACK_NAME -t template.yaml --s3-bucket $S3_BUCKET --capabilities CAPABILITY_IAM'
          dir ('hello-world') {
            sh 'npm ci'
            sh 'npm run integ-test'
          }
        }
      }
    }
    stage('prod') {
      environment {
        STACK_NAME = 'sam-app-prod-stage'
        S3_BUCKET = 'dfox-sam-jenkins-demo-us-east-1'
      }
      steps {
        withAWS(credentials: 'sam-jenkins-demo-credentials', region: 'us-east-1') {
          unstash 'aws-sam'
          sh 'sam deploy --stack-name $STACK_NAME -t template.yaml --s3-bucket $S3_BUCKET --capabilities CAPABILITY_IAM'
        }
      }
    }
  }
}

