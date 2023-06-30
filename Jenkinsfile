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
                        timeout(time: 1, unit: 'MINUTES') {
                            waitForQualityGate abortPipeline: true
                        }
                }
            }
        


        stage('Deployment') {
                    steps{
                        echo 'Deployment to UAT'
                    }
                }
            
                
            
        


    }
}
