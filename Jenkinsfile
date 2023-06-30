pipeline {
    agent {label 'slavenode'} 

    stages {
        stage('Build') {
            steps {
                echo 'Build'
                sh 'mvn package'
            }
        }
        stage('checking branch'){
            steps{
                script{
                    if (env.BRANCH_NAME == 'main'){
                        echo 'this is main branch'
                    }
                    else{
                        echo "this is ${env.BRANCH_NAME}"
                    }
                }
            }
        }
        stage("SonarQube analysis") {

            when{
                anyOf{
                    branch 'feature/*'
                }
            }
            steps {
              withSonarQubeEnv('sonar') {
                sh 'mvn sonar:sonar'
              }
            }
          }

        stage("Quality Gate") {
            steps {
                script {
                    try {
                        timeout(time: 1, unit: 'MINUTES') {
                            waitForQualityGate abortPipeline: true
                        }
                    } catch (Exception e) {
                        echo "Quality Gate failed: ${e.getMessage()}"
                    }
                }
            }
        }


        stage('Deployment') {
            parallel{
                stage('UAT Deployment'){
                    steps{
                        echo 'Deployment to UAT'
                    }
                }
            
                stage('Test Deployment'){
                    steps{
                        echo 'Deployment to test'
                    }
                }
            }
        }


    }
}
