pipeline {
    agent { label 'agent1' }
    environment {
        ARTIFACTORY_ACCESS_TOKEN = credentials('artifactory')
    }
    tools {
        maven '3.8.6'
    } 
    
    stages {
        stage ('Clean') {
            steps {
                sh 'mvn clean'
            }
        }

        stage('Build & SonarQube analysis') {
            steps {
                withSonarQubeEnv(installationName: 'sq') {
                    sh 'mvn package sonar:sonar'
                }
            }
        }

         stage("Quality gate") {
          steps {
                timeout(time: 3, unit: 'MINUTES') {
                        waitForQualityGate abortPipeline: true
                        }
          }
       }

        stage('Upload To Artifactory') {
            steps {
                sh 'jf rt upload --url http://172.31.77.161:8082/artifactory --access-token ${ARTIFACTORY_ACCESS_TOKEN} target/Calculator-1.0-SNAPSHOT.jar jenkins-maven-sonar/'

            }
        }
    }

    post {
        success {
            emailext attachLog: true, body: 'Pipeline Success !', subject: 'Pipeline Status', to: 'saharyadgar12@gmail.com'
        }
            
        failure {
            emailext attachLog: true, body: 'Pipeline failed...', subject: 'Pipeline Status', to: 'saharyadgar12@gmail.com'       
        }
        
        always {
            deleteDir()
        }
    }
}
