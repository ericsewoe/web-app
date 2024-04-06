COLOR_MAP = [
    'SUCCESS': 'good',
    'FAILURE': 'danger',
]

pipeline {
    agent any
    tools {
        maven "maven3.9.6"
    }

    stages {
        stage ("Git clone") {
            steps {
                git branch: 'main', url: 'https://github.com/ericsewoe/web-app.git'
            }
        }

        stage ("Build with Maven") {
            steps {
                sh "mvn clean"
            }
        }

        stage ("Testing with Maven") {
            steps {
                sh "mvn test"
            }
        }

        stage ("Package with Maven") {
            steps {
                sh "mvn package"
            }
        }

        stage('SonarQube Analysis') {
            environment {
                ScannerHome = tool 'sonar5.0'
            }
            steps {
                script {
                    withSonarQubeEnv('sonarqube') {
                        sh '${ScannerHome}/bin/sonar-scanner -Dsonar.projectKey=jomacs'
                    }
                }
            }
        }

        stage("Quality Gate") {
            steps {
              timeout(time: 1, unit: 'HOURS') {
                waitForQualityGate abortPipeline: true
              }
            }
        }

        stage ("Upload to Nexus") {
            steps {
                nexusArtifactUploader artifacts: [[artifactId: 'maven-web-application', classifier: '', file: '/var/lib/jenkins/workspace/first-pipeline-job/target/web-app.war', type: 'war']], credentialsId: 'nexus-id', groupId: 'com.mt', nexusUrl: '18.118.138.98:8081/', nexusVersion: 'nexus3', protocol: 'http', repository: 'webapp-release', version: '3.0.6-RELEASE'
            }
        }

        stage ("Deploy to UAT") {
            steps {
                deploy adapters: [tomcat9(credentialsId: 'tomcat-credential', path: '', url: 'http://3.138.193.107:8080')], contextPath: null, war: 'target/*.war'
            }
        }
    }

    post {
        success {
            slackSend channel: 'ci-cd', color: 'good', message: "Build successful: ${currentBuild.fullDisplayName}"
        }
        failure {
            slackSend channel: 'ci-cd', color: 'danger', message: "Build failed: ${currentBuild.fullDisplayName}"
        }
    }

}