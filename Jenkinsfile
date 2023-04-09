import com.amazonaws.services.ec2.model.TerminateInstancesRequest

pipeline {
  agent any

  stages {
    stage('run tests') {
      steps {
        script {
          def shardCount = 4
          def parallelStages = [:]
          for (int i = 1; i <= shardCount; i++) {
            def shard = "${i}/${shardCount}"
            parallelStages["Shard ${shard}"] = {
              node('kube') {
                docker.image('mcr.microsoft.com/playwright:v1.32.0-focal').inside {
                  sh '''
                      npm i -D @playwright/test
                      npx playwright install
                      npx playwright test --workers=2 --headed --max-failures=1 --project=foo --shard ${shard}
                  '''
                }
              }
            }
          }
          parallel parallelStages
        }
      }
    }
  }
}
