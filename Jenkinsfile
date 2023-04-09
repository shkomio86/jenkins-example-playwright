pipeline {
  agent any
  stages {
    stage('Run tests') {
      steps {
        sh '''
          npm i -D @playwright/test
          npx playwright install
        '''
      }
      parallel {
        branch1: {
          steps {
            sh '''
              npx playwright test -- --shard 1/3
            '''
          }
        }
        branch2: {
          steps {
            sh '''
              npx playwright test -- --shard 2/3
            '''
          }
        }
        branch3: {
          steps {
            sh '''
              npx playwright test -- --shard 3/3
            '''
          }
        }
      }
    }
  }
  post {
      always {
        script {
          def instanceIds = awsEc2DescribeInstances().reservations.findAll {
            it.instances*.tags*.value.contains('kube')
          }*.instances*.instanceId
          if (!instanceIds.empty) {
            sh "aws ec2 terminate-instances --instance-ids ${instanceIds.join(' ')} --region ${AWS_REGION}"
          } else {
            echo "No 'kube' instances found to terminate."
          }
        }
      }
    }
}
