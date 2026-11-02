
pipeline {
    agent any

    environment {
        AWS_REGION = 'us-east-1'
        AWS_ACCOUNT_ID = '613591981591'
        ECR_REPOSITORY = 'jenkins-docker-ecr-pipeline'
        IMAGE_NAME = 'jenkins-docker-ecr-pipeline'
        ECR_REGISTRY = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com"
    }

    stages {
        stage('Source Code Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Docker Image Build') {
            steps {
                sh '''
                    docker build -f dockerfile \
                    -t ${IMAGE_NAME}:${BUILD_NUMBER} .
                    docker tag ${IMAGE_NAME}:${BUILD_NUMBER} \
                    ${ECR_REGISTRY}/${ECR_REPOSITORY}:${BUILD_NUMBER}
                '''
            }
        }

        stage('Push Image to Amazon ECR') {
            steps {
                sh '''
                    aws ecr get-login-password --region ${AWS_REGION} |
                    docker login --username AWS --password-stdin ${ECR_REGISTRY}

                    docker push ${ECR_REGISTRY}/${ECR_REPOSITORY}:${BUILD_NUMBER}
                '''
            }
        }
    }
}
