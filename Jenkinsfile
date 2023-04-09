import echosign.orchestration.*
import echosign.wrapper.*
import echosign.jenkins.*

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
        sh '''
          npx playwright test
        '''
      }
    }
    stage('Initialize') {
        steps {
            script {
                def shardCount = 4
                env.SHARD_COUNT = shardCount.toString()
            }
        }
    }
    stage('Run Tests') {
        steps {
            script {
                def shardCount = env.SHARD_COUNT.toInteger()
                def parallelStages = [:]
                for (int i = 1; i <= shardCount; i++) {
                    def shard = "${i}/${shardCount}"
                    parallelStages["Shard ${shard}"] = {
                        agent {
                            label "kube"
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
    }
  }
}
