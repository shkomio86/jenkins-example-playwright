pipeline {
  agent any
  stages {
    stage('Run Tests') {
      steps {
        script {
          def shardCount = 4
          parallelStages = [:]
          for (int i = 1; i <= shardCount; i++) {
            def shard = "${i}/${shardCount}"
            parallelStages["Shard ${shard}"] = {
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
          parallel parallelStages
        }
      }
    }
  }
}