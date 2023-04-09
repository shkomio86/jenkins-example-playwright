def numAgents = 2
def numExecutorsPerAgent = 1
def numInstances = numAgents * numExecutorsPerAgent
def labelPrefix = 'kube'
pipeline {
  agent any
  stages {
    stage('Run Playwright tests') {
      steps {
        script {
            def parallelStages = [:]
            for (int i = 1; i < numAgents + 1; i++) {
              def agentLabel = "${labelPrefix}${i}"
              def agentIndex = i
              parallelStages["Shard ${agentLabel}"] = {
                node(agentLabel) {
                  docker.image('mcr.microsoft.com/playwright:v1.17.0-focal').inside {
                    sh '''
                        npm i -D @playwright/test
                        npx playwright install
                        npx playwright test --shard=${agentIndex}/${numInstances} --workers=${numExecutorsPerAgent}
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
}