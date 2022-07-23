pipeline {
    agent any
    environment{   
        AWS_S3_BUCKET = "jenkins-bucket"
        ARTIFACT_NAME = "hello-world.dll"
        AWS_ACCESS_KEY_ID     = credentials(' ')
        AWS_SECRET_ACCESS_KEY = credentials(' ')
        AWS_EB_APP_NAME = "jenkinsapp"
        AWS_EB_APP_VERSION = "${BUILD_ID}"
        AWS_EB_ENVIRONMENT = "jenkinsenv"
    }

    stages {
        stage('Validate') {
            steps {
                sh "dotnet restore"
                
            }
        }

        stage('Build') {
            steps {
                sh "dotnet build"
            }
        }

        stage('Test') {
            steps {
                sh "dotnet test --logger:junit"
                
            }
  
        }
        stage('Package') {
            steps {
                sh "dotnet publish"
                
            }
            post{
                success{
                    archiveArtifacts artifacts: '**/bin/Debug/net6.0/**.dll', followSymlinks: false
                }
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
