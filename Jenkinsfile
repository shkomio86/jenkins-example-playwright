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
    stage('test') {
      steps {
        sh '''
          npx playwright test
        '''
      }
    }
    stage('test') {
      parallel {
        stage('Test shard 1') {
            steps {
                sh 'npx playwright test --shard 1/2 --shard-count 2'
            }
        }
        stage('Test shard 2') {
            steps {
                sh 'npx playwright test --shard 2/2 --shard-count 2'
            }
        }
      }
    }
  }
}
