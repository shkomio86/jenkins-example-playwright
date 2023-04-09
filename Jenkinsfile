pipeline {
  agent {
    docker {
      image 'mcr.microsoft.com/playwright:v1.32.0-focal'
      label 'kube'
    }
  }
  stages {
    stage('check versions') {
      steps {
        sh '''
          node --version || true
          npm --version || true
        '''
      }
    }
    stage('install playwright') {
      steps {
        sh '''
          npm i -D @playwright/test
          npx playwright install
        '''
      }
    }
    stage('test1') {
      steps {
        script {
          def shardCount = 4
          def parallelStages = [:]
          for (int i = 1; i <= shardCount; i++) {
            def shard = "${i}/${shardCount}"
            parallelStages["Shard ${shard}"] = {
              node {
                label 'kube'
                // Pokreće se test za trenutni shard
                sh "npx playwright test --workers 1 --shard ${shard} --headed --headed-timeout 300000 --timeout 300000 --repeat-each 2 --project-root . --video artifacts/videos/shard${shard} --output artifacts/json/shard${shard} --reporter junit --reporter json"
              }
            }
          }
          parallel parallelStages
        }
      }
    }
    stage('test1') {
      steps {
        sh '''
          npx playwright test
        '''
      }
    }
    post {
        always {
          script {
            def instanceIds = awsEc2DescribeInstances().reservations.findAll {
              it.instances*.tags*.value.contains(JOB_NAME)
            }*.instances*.instanceId
            if (!instanceIds.empty) {
              sh "aws ec2 terminate-instances --instance-ids ${instanceIds.join(' ')} --region ${AWS_REGION}"
            } else {
              echo "No '${JOB_NAME}' instances found to terminate."
            }
          }
        }
   }
  }
}
