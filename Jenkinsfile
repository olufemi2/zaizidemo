pipeline {
    agent any
    environment {
        AWS_ACCOUNT_ID="301221094073"
        AWS_DEFAULT_REGION="us-east-1" 
        IMAGE_REPO_NAME="zaizi-jenkins-pipeline-demo"
        IMAGE_TAG="latest"
        REPOSITORY_URI = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${IMAGE_REPO_NAME}"
    }  

    stages {

         stage('Logging into AWS ECR') {
            steps {
                script {
                sh "aws ecr get-login-password --region ${AWS_DEFAULT_REGION} | docker login --username AWS --password-stdin ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com"
                }
                 
            }
        }

        stage('Cloning Git') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '^feature1|Prod|test.*$']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/olufemi2/zaizidemo.git']]])
            }
        }

        # Building Docker images
    stage('Building image') {
      steps{
        script {
          dockerImage = docker.build "${IMAGE_REPO_NAME}:${IMAGE_TAG}"
        }
      }
    }

    stages {
        stage('Performance Testing') {
            steps {
                echo 'Installing k6'
                sh 'sudo chmod +x setup_k6.sh'
                sh 'sudo ./setup_k6.sh'
                echo 'Running K6 performance tests...'
                sh 'k6 run loadtests/performance-test.js'
            }
         }
        }
    
        # Uploading Docker images into AWS ECR
    stage('Pushing to ECR') {
     steps{  
         script {
                sh "docker tag ${IMAGE_REPO_NAME}:${IMAGE_TAG} ${REPOSITORY_URI}:$IMAGE_TAG"
                sh "docker push ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${IMAGE_REPO_NAME}:${IMAGE_TAG}"
         }
        }
      }

}
