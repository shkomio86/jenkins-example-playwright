#!groovy

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
          echo 11111111111
        '''
      }
    }
    stage('Initialize') {
        steps {
            // Set the shard count
            script {
                def shardCount = 3
                env.SHARD_COUNT = shardCount.toString()
            }
        }
    }
    stage('Run Tests') {
        parallel {
            // Run tests on each agent node
            for (int i = 1; i <= env.SHARD_COUNT.toInteger(); i++) {
                def shard = "${i}/${env.SHARD_COUNT}"

                stage("Shard ${shard}") {
                    agent {
                        label "aws-node-${i}"
                    }
                    steps {
                        sh "npm install"
                        sh "npx playwright test -- --shard ${shard}"
                    }
                }
            }
        }
    }
  }
}
