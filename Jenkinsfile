pipeline {
  agent {
    docker { 
      image 'mcr.microsoft.com/playwright:v1.17.2-focal'
      label 'kube'
    }
  }
  options {
    ansiColor('xterm')
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
  }
}
