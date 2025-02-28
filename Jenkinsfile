pipeline {
    agent any
    
    tools{
        jdk 'jdk17'
        maven 'maven3'
        
    }
    
    environment {
        SCANNER_HOME= tool 'sonar-scanner'
        
    }
    
    stages {
        stage('Git Checkout') {
            steps {
                git branch: 'main', changelog: false, poll: false, url: 'https://github.com/nararm1911/secretsanta.git'
            }
        }
        
        stage('Compile') {
            steps {
                sh "mvn clean compile"
            }
        }
        
        stage('Sonarqube-Analiysis') {
            steps {
                sh  '''
                $SCANNER_HOME/bin/sonar-scanner \
                        -Dsonar.url=http://167.172.182.8:9000 \
                        -Dsonar.login=squ_20626649d6ac90c080c7ad0f30b3f03b33ee4335 \
                        -Dsonar.projectName=secret-santa \
                        -Dsonar.java.binaries=. \
                        -Dsonar.projectKey=secret-santa 
                '''
                
                
            }
        }
        
        stage('Build') {
            steps {
                sh "mvn clean install"
            }
        }
        
        stage('Docker Build & Push') {
            steps {
                script { 
                    withDockerRegistry(credentialsId: '2ea37e02-ffd3-4b30-9ef4-e8c89f171c07', toolName: 'docker') { 
                        
                        sh "docker build -t santa ."
                        sh "docker tag santa nararm1911/santa:latest"
                        sh "docker push nararm1911/santa:latest"
                    }
                    
                }
            }
        }
        
        stage('Docker Deploy to Container') {
            steps {
                script { 
                    withDockerRegistry(credentialsId: '2ea37e02-ffd3-4b30-9ef4-e8c89f171c07', toolName: 'docker') { 
                        
                        sh "docker run -d --name santa -p 8080:8080 nararm1911/santa:latest"
                    }
                    
                }
            }
        }
        
       
    }
    
}
        
