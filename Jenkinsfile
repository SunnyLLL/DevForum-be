pipeline {
    agent any
    environment {
        AWS_ACCOUNT_ID="713495193197"
        AWS_DEFAULT_REGION="ap-southeast-2" 
        IMAGE_REPO_NAME="rentalreadybackend"
        IMAGE_TAG="latest"
        AWS_ECS_CLUSTER="RentalReadyCluster"
        AWS_ECS_SERVICE="RentalReadyService"
        REPOSITORY_URI = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${IMAGE_REPO_NAME}:${IMAGE_TAG}"
    }
         //install docker,cli ,git in jenkins service
    stages {
        stage('Git checkout') {
            steps{
                // Get source code from a GitHub repository
               checkout([$class: 'GitSCM', branches: [[name: '*/dev']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/SunnyLLL/DevForum-be.git']]])
            }
        }
        
        stage('Build docker image') {
            steps{

                    sh 'docker build -t ${IMAGE_REPO_NAME}:${IMAGE_TAG} .'
                }
            }
        
        stage('ECR login') {
            steps{
                    sh 'aws ecr get-login-password --region ${AWS_DEFAULT_REGION} | docker login --username AWS --password-stdin ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com'
                }
            }
        
        
        stage('Push image to ECR') {
            steps{
                    sh 'docker tag ${IMAGE_REPO_NAME}:${IMAGE_TAG} ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${IMAGE_REPO_NAME}:${IMAGE_TAG}'

                    sh 'docker push ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${IMAGE_REPO_NAME}:${IMAGE_TAG}'
                 }   
             }
        
         stage('Deploy in ECS') {
            steps {
                    script {
                        // AWS CLI must be installed in the Jenkins server first. 
                        // Below is used to upgrade/replace the existing service, which may be created manually or through terraform.
                        echo "=========== Upgrade ECS cluster's service state with forceNewDeployment================="
                        sh("aws ecs update-service --cluster ${AWS_ECS_CLUSTER} --service ${AWS_ECS_SERVICE} --force-new-deployment")
                    }
                }
            }
         }
  }
        
