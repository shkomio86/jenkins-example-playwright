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
        '''
      }
    }
    stage('Initialize') {
        steps {
            script {
                def shardCount = 3
                env.SHARD_COUNT = shardCount.toString()
            }
        }
    }
  }
}
