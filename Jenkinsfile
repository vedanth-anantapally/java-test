
pipeline {
    agent {label 'slavenode'} 

    parameters{
        string(name: 'Rollbackversion', description: 'Enter your rollback version')
        choice{
            choices: ['Dev', 'UAT', 'Prod']
            name: 'Environment'
            description: 'Select your environment'
        }
    }

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
                            def qualityGate = waitForQualityGate abortPipeline: true
                            echo "status is {$qualityGate}"
                            echo "gate details are {$qualityGate}"
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

                stage('Production Deployment'){
                    
                    when{
                        expression{ return params.Environment == 'Prod'}
                    }


                    steps{
                        input(
                            message: 'Agreed to deploy this build for production'
                        )
                        echo 'Deployment to Production post approval'
                    }
                }
            }
        }

    post{

        always{
            echo "Always"

            mail (to:'ananthapally.vedanth@gmail.com', subject: 'notification for always', body: 'notification for always')

            echo "${BUILD_NUMBER}"
            echo "${BUILD_ID}"
            echo "${JOB_NAME}"

        }

        failure{
            echo "failure"

            mail (to:'ananthapally.vedanth@gmail.com', subject: 'notification for failure', body: 'notification for failure')

            echo "${BUILD_NUMBER}"
            echo "${BUILD_ID}"
            echo "${JOB_NAME}"
        }

        abort{
            echo "abort"

            mail (to:'ananthapally.vedanth@gmail.com', subject: 'notification for abort', body: 'notification for abort')
            
            echo "${BUILD_NUMBER}"
            echo "${BUILD_ID}"
            echo "${JOB_NAME}"
        }
    }


    }
}
