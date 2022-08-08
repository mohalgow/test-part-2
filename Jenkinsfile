pipeline {
    agent any

    environment {

        AWS_ACCESS_KEY_ID     = credentials('m.algow-aws-secret-key-id')
        AWS_SECRET_ACCESS_KEY = credentials('m.algow-aws-secret-access-key')

        AWS_S3_BUCKET = "m.algow-belt2d2-artifacts-123456"
        ARTIFACT_NAME = "hello-world.war"
        AWS_EB_APP_NAME = "test-p2"
        AWS_EB_APP_VERSION = "${BUILD_ID}"
        AWS_EB_ENVIRONMENT = "Testp2-env"

        SONAR_IP = "52.23.193.18"
        SONAR_TOKEN = "sqp_375a0552b08ba037d07ab9858500bc40fe78b8f6"

    }

    stages {
        stage('Validate') {
            steps {
                
                sh "mvn validate"

                sh "mvn clean"

            }
        }

         stage('Build') {
            steps {
                
                sh "mvn compile"

            }
        }

        stage('Test') {
            steps {
                
                sh "mvn test"

            }

            post {
                always {
                    junit '**/target/surefire-reports/TEST-*.xml'
                }
            }
        }

        stage('Quality Scan'){
            steps {
                sh '''

                mvn clean verify sonar:sonar \
                    -Dsonar.projectKey=onsite-M.ALGOW-B2D2 \
                    -Dsonar.host.url=http://52.23.193.18 \
                    -Dsonar.login=sqp_375a0552b08ba037d07ab9858500bc40fe78b8f6

                '''
            }
        }

    
        stage('Deploy') {
            steps {

                sh 'aws elasticbeanstalk create-application-version --application-name $AWS_EB_APP_NAME --version-label $AWS_EB_APP_VERSION --source-bundle S3Bucket=$AWS_S3_BUCKET,S3Key=$ARTIFACT_NAME'

                sh 'aws elasticbeanstalk update-environment --application-name $AWS_EB_APP_NAME --environment-name $AWS_EB_ENVIRONMENT --version-label $AWS_EB_APP_VERSION'
            
                
            }
        }
        
    }
}
