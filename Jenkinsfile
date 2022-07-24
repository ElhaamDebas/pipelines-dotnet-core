pipeline {
    agent any

    environment {
        AWS_ACCESS_KEY_ID     = credentials('aws-access-key-id')
        AWS_SECRET_ACCESS_KEY = credentials('aws-secret-access-key')

        
    }
    stages {
        stage('Restore') {
            steps {

                sh 'dotnet restore'

            }
        }
    
  
        stage('Bulid') {
            steps {

                sh "dotnet build"
            }
        }
    

        stage('Test') {
            steps {

                sh "dotnet test"
            }
        }
        stage('Package') {
            steps {

                sh "dotnet publish"
            }

            post {
                success {
                    archiveArtifacts artifacts: 'bin/Debug/net6.0/pipelines-dotnet-core.dll', followSymlinks: false
                }
            }
        }
    }
}