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

        stage('uploads to nexus'){
            steps{
                nexusArtifactUploader artifacts: [[artifactId: 'maven-web-application', classifier: '', file: '/var/lib/jenkins/workspace/jomacs-webapp-jenkinsfile/target/web-app.war', type: 'war']], credentialsId: 'nexus-credentials', groupId: 'com.mt', nexusUrl: '3.143.141.63:8081/repository/jomacs-webapp', nexusVersion: 'nexus3', protocol: 'http', repository: 'jomacs-webapp', version: '3.0.6-RELEASE'
            }
        }

        stage('prod deployment'){
            steps{
                deploy adapters: [tomcat9(credentialsId: 'tomcat-credentials', path: '', url: 'http://3.137.191.63:8080')], contextPath: null, war: 'target/web-app.war'
            }
        }
    }
}