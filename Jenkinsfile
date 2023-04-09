pipeline {
    agent any
    
    stages {
        stage('Run Tests in Parallel') {
            steps {
                script {
                    def shardCount = 4
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
        }
    }
}