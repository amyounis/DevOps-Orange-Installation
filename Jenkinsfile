pipeline {
    environment {
        
        imageName = "toy0store"
        registryCredentials = "nexus"
        registry = "192.168.1.3:8123/repository/docker-private"
        dockerImage = ''
    
        
    }
    agent any

    tools {

        maven "maven Install"
        dockerTool "DokerInstall"
    }

    stages {
        stage('Build') {
            steps {
                // Get some code from a GitHub repository
                
                git branch: 'main',
                    url: 'https://github.com/Eman-Github/My-Devops-Orange.git'
            
                sh "pwd"
                
                sh "ls /Users/emanabdelaty/.jenkins/workspace/toystore-pipeline"
                
                sh '''
                    cd /Users/emanabdelaty/.jenkins/workspace/toystore-pipeline/Toy0Store
                    mvn -Dmaven.test.failure.ignore=true clean package
                '''
            }

            post {
                // If Maven was able to run the tests, even if some of the test
                // failed, record the test results and archive the jar file.
                success {
                    //junit '**/target/surefire-reports/TEST-*.xml'
                    archiveArtifacts 'Toy0Store/target/*.jar'
                    
                }
            }
        }
        stage('Building image') {
          steps{
            script {
                
              dockerImage = docker.build imageName + ":$Devops-Orange-Tags"   
            }
          }
        }
         
        stage('Uploading docker image to Nexus') {
         steps{  
             script {
                 docker.withRegistry( 'http://'+registry, registryCredentials ) {
                 dockerImage.push(imageName + ":$Devops-Orange-Tags")
              }
            }
          }
        }
    }
}

