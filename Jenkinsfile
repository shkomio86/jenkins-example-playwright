import com.amazonaws.services.ec2.model.TerminateInstancesRequest

pipeline {
  agent any
  stages {
      stage('run tests') {
        def shardCount = 4
        parallel {
          for (int i = 1; i <= shardCount; i++) {
            def shard = "${i}/${shardCount}"
            stage("Shard ${shard}")  {
                agent {
                  docker {
                    image 'mcr.microsoft.com/playwright:v1.32.0-focal'
                    label 'kube'
                  }
                }
                steps {
                  sh '''
                      npm i -D @playwright/test
                      npx playwright install
                      npx playwright test --workers=2 --headed --max-failures=1 --project=foo --shard ${shard}
                  '''
                }
            }
          }
        }
     }
  }
}
