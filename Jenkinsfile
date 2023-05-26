pipeline {
    agent any 
    
    tools{
        jdk 'jdk11'
        maven 'maven3'
    }
    
    environment {
        SCANNER_HOME=tool 'sonar-scanner'
    }
    
    stages{
        
        stage("Git Checkout"){
            steps{
                git branch: 'master', changelog: false, poll: false, url: 'https://github.com/naresh9919/secretsanta-generator.git'
            }
        }
        
        stage("Compile"){
            steps{
                sh "mvn clean compile"
            }
        }
        
         stage("Test Cases"){
            steps{
                sh "mvn test"
            }
        }
        
        stage("Sonarqube Analysis "){
            steps{
                withSonarQubeEnv('sonar-server') {
                    sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=secretsanta \
                    -Dsonar.java.binaries=. \
                    -Dsonar.projectKey=secretsanta '''
    
                }
            }
        }

        stage("OWASP Dependency Check"){
            steps{
                dependencyCheck additionalArguments: '--scan ./ ', odcInstallation: 'DP-Check'
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }
        
         stage("Build"){
            steps{
                sh " mvn clean install"
            }
        }

        stage('Build docker santa image'){
            steps{
                script{
                    sh 'docker build -t nareshbabu1991/secretsanta .'
                }
            }
        }

        stage("Docker Build & Push"){
            steps{
                script{
                   withDockerRegistry(credentialsId: 'dockerhub-cred', toolName: 'docker') {
                        
                        sh "docker tag nareshbabu1991/secretsanta nareshbabu1991/secretsanta:latest "
                        sh "docker push nareshbabu1991/secretsanta:latest "
                    }
                }
            }
        }
    }
}
