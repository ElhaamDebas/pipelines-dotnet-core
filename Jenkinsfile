pipeline {
    agent any
    stages {
        stage('Download the packages') {
            steps {

                bat 'dotnet restore'

                bat 'dotnet clean'

            }
        }
    
  
        stage('Bulid') {
            steps {

                bat 'dotnet build'
            }
        }
    

        stage('Test') {
            steps {

                bat 'dotnet test'
            }

            post {
                always {
                    junit '**/target/surefire-reports/TEST-*.xml'
                }
            }
        }
    
    
        stage('Package') {
            steps {

                bat 'dotnet package'
            }


            post {
                success {
                    archiveArtifacts artifacts: '**/bin/Debug/net6.0/*.dll', followSymlinks: false
                }
            }
        }
    }
}
