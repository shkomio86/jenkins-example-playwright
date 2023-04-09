def numAgents = 2
def numExecutorsPerAgent = 4
def numInstances = numAgents * numExecutorsPerAgent
def labelPrefix = 'kube'

pipeline {
  agent none
  stages {
    stage('Run Playwright tests') {
      steps {
        script {
          parallel {
            for (int i = 1; i < numAgents + 1; i++) {
              def agentLabel = "${labelPrefix}${i}"
              def agentIndex = i
              stage("Agent ${agentIndex}") {
                agent {
                  docker {
                    image 'mcr.microsoft.com/playwright:v1.32.0-focal'
                    label agentLabel
                  }
                }
                steps {
                  sh """
                    npm i -D @playwright/test
                    npx playwright install
                    npx playwright test --shard --shard-count ${numInstances} --shard-index ${agentIndex} --workers ${numExecutorsPerAgent} --headed
                  """
                }
              }
            }
          }
        }
      }
    }
  }
}