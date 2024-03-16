pipeline {
   agent any
   environment {
        // scannerHome = tool "sonar-scanner"
        AWS_ACCOUNT_ID="381492085690"
        AWS_DEFAULT_REGION="ap-south-1"
        IMAGE_REPO_NAME="dev"
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
    //     stage('Static code Analisys'){
    //         steps {
    //         script{
    //            def scannerHome = tool 'sonar-scanner';
    //            withSonarQubeEnv() {
    //                 sh "${scannerHome}/bin/sonar-scanner"
    //             }
    //         }
    //     }    
            
    // }
    	stage('Logging into AWS ECR') {
            steps {
	      script {
               sh """aws ecr get-login-password --region ${AWS_DEFAULT_REGION} | docker login --username AWS --password-stdin ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com"""
              // sh "aws ecr get-login-password --region ap-south-1 | docker login --username AWS --password-stdin 547013421517.dkr.ecr.ap-south-1.amazonaws.com"
            } 

        }
       }
    stage('Building image') {
            steps{
            script {
                dockerImage = docker.build "${IMAGE_REPO_NAME}:${IMAGE_TAG}"
            }
        }
    }
    stage('Pushing to ECR') {
	steps{
	script {
             sh "docker tag ${IMAGE_REPO_NAME}:${IMAGE_TAG} ${REPOSITORY_URI}:$IMAGE_TAG"
             sh "docker push ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${IMAGE_REPO_NAME}:${IMAGE_TAG}"
	     }
	}
    }
    // stage('Trigger Update K8s') {
    //         steps{
    //         script {
    //             echo "triggering Update manifest Job"
    //             build job: 'Frontend-Deployment', parameters: [string(name: 'DOCKERTAG', value: env.BUILD_NUMBER)]
    //         }
    //     }
    // }
	stage ("Deploy to K8S") {
            steps {
                withKubeConfig(caCertificate: '', clusterName: '', contextName: '', credentialsId: 'Kubernetes', namespace: '', restrictKubeConfigAccess: false, serverUrl: '') {
                 sh "kubectl apply -f deployment.yaml"
                }
            }
        }
	      
   }
}
