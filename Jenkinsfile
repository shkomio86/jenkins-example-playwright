import com.amazonaws.services.ec2.model.TerminateInstancesRequest

pipeline {
  agent any
  stages {
    stage('run tests') {
        parallel {
              def shardCount = 2
              for (int i = 1; i <= shardCount; i++) {
                def shard = "${i}/${shardCount}"
                parallelStages["Shard ${shard}"] = {
                  node('kube') {
                    docker.image('mcr.microsoft.com/playwright:v1.32.0-focal').inside {
                      sh '''
                          npm i -D @playwright/test
                          npx playwright install
                          npx playwright test --workers=2 --headed --max-failures=1 --shard ${shard}
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
