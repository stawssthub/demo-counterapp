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

        stage('upload war files to nexus'){
            steps{
                script{
                    def readPomVersion = readMavenPom file: 'pom.xml'

                    def nexusRepo = readPomVersion.version.endsWith("SNAPSHOT") ? "demoapp-snapshot" : "demoapp-release"
                    nexusArtifactUploader artifacts: 
                    [[artifactId: 'springboot', 
                    classifier: '', file: 'target/Uber.jar', 
                    type: 'jar']
                    ], 
                    credentialsId: 'nexus-api', 
                    groupId: 'com.example', 
                    nexusUrl: 'ec2-34-229-90-52.compute-1.amazonaws.com:8081', 
                    nexusVersion: 'nexus3', 
                    protocol: 'http', 
                    repository: nexusRepo, 
                    version: "${readPomVersion.version}"
                }
            }
        }

        stage('Docker Image Build'){
            steps{
                script{
                    sh 'docker image build -t $JOB_NAME:v1.I$BULD_ID .'
                    sh 'docker image tag $JOB_NAME:v1.$BUILD_ID sunildckr/$JOB_NAME:v1.$BUILD_ID'
                    sh 'docker image tag $JOB_NAME:v1.$BUILD_ID sunildckr/$JOB_NAME:latest'
                }
            }
        }

        /*stage('push to the dockerHub'){
            steps{
                script{
                    withCredentials([string(credentialsId: 'docker_hub_api', variable: 'docker_hub_secret')]) {
                     sh 'docker login -u sunildckr -p ${docker_hub_secret}'
                     sh 'docker image push sunildckr/$JOB_NAME:v1.$BUILD_ID'
                     sh 'docker image push sunildckr/$JOB_NAME:latest'
                    }   
                }
            }
        } */
    }
}