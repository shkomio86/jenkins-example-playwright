import echosign.orchestration.*
import echosign.wrapper.*
import echosign.jenkins.*

pipeline {
  script {
      def shardCount = 4
      env.SHARD_COUNT = shardCount.toString()
      def shardCount = env.SHARD_COUNT.toInteger()
      def parallelStages = [:]
      for (int i = 1; i <= shardCount; i++) {
          def shard = "${i}/${shardCount}"
          parallelStages["Shard ${shard}"] = {
              docker {
                image 'mcr.microsoft.com/playwright:v1.32.0-focal'
                label 'kube'
              }
              stages {
                  stage('Run Tests') {
                      steps {
                          sh "npm install"
                          sh "npx playwright test -- --shard ${shard}"
                      }
                  }
              }
          }
      }
      parallel parallelStages
  }
}
