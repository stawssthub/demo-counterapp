pipeline {
    agent any
    tools {
        maven 'maven-3.9'
    }
    
    stages{
        stage('checkout code'){
            steps{
                git branch: 'main', url: 'https://github.com/stawssthub/demo-counterapp.git'
            }
        }
        
        stage('Unit Testing'){
            steps{
                script{
                    sh "mvn test"
                }
            }
        }
        
        stage('Integration testing'){
            steps{
                script{
                    sh 'mvn verify -DskipUnitTests'
                }
            }
        }

        stage('Maven build'){
            steps{
                script{
                    sh 'mvn clean install'
                }
            }
        }

        stage ('static code analasys'){
            steps{
                script{
                    withSonarQubeEnv(credentialsId: 'sonar-api-key') {
                        sh 'mvn clean package sonar:sonar'
                    }
                }
            }
        }

        stage('quality gate status'){
            steps{
                script{
                    waitForQualityGate abortPipeline: false, credentialsId: 'sonar-api-key'
                }
            }
        }
    }
}