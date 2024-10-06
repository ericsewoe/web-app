pipeline{
    agent any

    tools{
        maven 'maven'
    }

    stages{
        stage('git checkout'){
            steps{
                git branch: 'main', url: 'https://github.com/ericsewoe/web-app.git'
            }
        }

        stage('maven build'){
            steps{
                sh 'mvn clean package'
            }
        }

        stage('Code Analysis') {
            environment {
                ScannerHome = tool 'sonar'
            }
            steps {
                script {
                    withSonarQubeEnv('sonar') {
                        sh "${ScannerHome}/bin/sonar-scanner -Dsonar.projectKey=john-webapp"
                    }
                }
            }
        }
    }
}