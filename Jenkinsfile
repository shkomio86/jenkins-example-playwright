import com.amazonaws.services.ec2.model.TerminateInstancesRequest

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
        script {
          def shardCount = 1
          def parallelStages = [:]
          for (int i = 1; i <= shardCount; i++) {
            def shard = "${i}/${shardCount}"
            parallelStages["Shard ${shard}"] = {
              node {
                label 'kube'
                // Pokreće se test za trenutni shard
                sh "npx playwright test --workers 1"
              }
            }
          }
          parallel parallelStages
        }
      }
    }
    stage('test2') {
      steps {
        sh '''
          npx playwright test
        '''
      }
      post {
          always {
            script {
              // Terminate all instances with label 'kube'
              def label = "kube"
              def instances = awsEC2.describeInstances().getReservations().collectMany {
                it.getInstances()
              }.findAll {
                it.getLabels().contains(label)
              }
              instances.each {
                println "Terminating instance: ${it.getInstanceId()}"
                awsEC2.terminateInstances(new TerminateInstancesRequest().withInstanceIds(it.getInstanceId()))
              }
            }
          }
        }
    }
  }
}
