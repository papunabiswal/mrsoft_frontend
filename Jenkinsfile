pipeline {
   agent any
   environment {
        scannerHome = tool "sonarqube"
        AWS_ACCOUNT_ID="547013421517"
        AWS_DEFAULT_REGION="ap-south-1"
        IMAGE_REPO_NAME="frontend"
        IMAGE_TAG="${env.BUILD_NUMBER}"
        REPOSITORY_URI = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${IMAGE_REPO_NAME}"
   }

    stages {
        stage('Git Checkout') {
            steps {
            script{
                git branch: 'dev', credentialsId: 'GitHub', url: 'https://github.com/papunabiswal/mrsoft_frontend.git'  
                }  
            }
        }
        stage('Static code Analisys'){
            steps {
            script{
               def scannerHome = tool 'sonarqube';
               withSonarQubeEnv() {
                    sh "${scannerHome}/bin/sonar-scanner"
                }
            }
        }    
            
    }
    stage('Static code Analisys'){
            steps {
            script{
                def mvn = tool 'maven3.9.6';
                withSonarQubeEnv() {
                    sh "${mvn}/bin/mvn clean verify sonar:sonar -Dsonar.projectKey=mrsoft_Frontend"
                }
            }
        }    
            
    }	    
	      
   }
}
