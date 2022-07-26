pipeline {
    agent any
    environment{  
        AWS_ACCESS_KEY_ID     = credentials('jenkins-aws-access-key-id')
        AWS_SECRET_ACCESS_KEY = credentials('jenkins-aws-secret-access-key') 
        AWS_S3_BUCKET = "jenkins-web-server"
        ARTIFACT_NAME = "hello-world.dll"
        AWS_EB_APP_NAME = "dotnet-web-server"
        AWS_EB_APP_VERSION = "${BUILD_ID}"
        AWS_EB_ENVIRONMENT = "Dotnetwebserver-env"

        SONAR_IP = "54.226.50.200"
        SONAR_TOKEN ="sqp_90a1fed66968a246e178f32f22f7e3190973bc53"  
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
    
        stage('Quality Scan'){
            steps{
                sh '''
                dotnet sonarscanner begin /k:"Build-a-.NET-Application-with-Jenkins" /d:sonar.host.url="http://$SONAR_IP"  /d:sonar.login="$SONAR_TOKEN"
                dotnet build
                dotnet sonarscanner end /d:sonar.login="SONAR_TOKEN"
                '''
            }
        }
        stage('Publish artfacts to s3') {
            steps {
                sh "aws configure set region us-east-1"
                sh "aws s3 cp ./bin/Debug/net6.0/**.dll s3://$AWS_S3_BUCKET/$ARTIFACT_NAME"
            }
        }

        stage('Deploy') {
            steps {

                sh "aws elasticbeanstalk create-application-version --application-name $AWS_EB_APP_NAME --version-label $AWS_EB_APP_VERSION --source-bundle S3Bucket=$AWS_S3_BUCKET,S3Key=$ARTIFACT_NAME"

                sh "aws elasticbeanstalk update-environment --application-name $AWS_EB_APP_NAME --environment-name $AWS_EB_ENVIRONMENT --version-label $AWS_EB_APP_VERSION"
            }
        }
    }
}