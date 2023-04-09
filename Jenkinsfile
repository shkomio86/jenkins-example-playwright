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
    stage('test2') {
		parallel {
			// Define the number of shards to use
			def shardCount = 3
			
			// Run tests on each agent node
			for (int i = 1; i <= shardCount; i++) {
				def shard = "${i}/${shardCount}"
				
				stage("Shard ${shard}") {
					agent {
						label "kube"
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
