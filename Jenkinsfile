pipeline {
    agent any

    environment {
        function_name = 'java-sample'
    }

    stages {
        stage('Build') {
            steps {
                echo 'Build'
                sh 'mvn package'
            }
        }

        stage('Push') {
            steps {
                echo 'Push'

                sh "aws s3 cp target/sample-1.0.3.jar s3://bckfors3fromlambda"
            }
        }

        stage('Deploy') {
            steps {
                echo 'Build'

                sh "aws lambda update-function-code --function-name $function_name --s3-bucket s3://bckfors3fromlambda --s3-key sample-1.0.3.jar"
            }
        }
    }
}
