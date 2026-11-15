pipeline {
    agent any

    environment {
        AWS_REGION = 'ap-south-2'
        ECR_REPO = '546517268801.dkr.ecr.ap-south-2.amazonaws.com/hotel-booking'
        VITE_API_BASE_URL = 'http://18.61.80.207:5001'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Images') {
            steps {
                sh '''
                    docker build -f hotel-booking-frontend/Dockerfile \
                    --build-arg VITE_API_BASE_URL=$VITE_API_BASE_URL \
                    -t $ECR_REPO:frontend-$BUILD_NUMBER .

                    docker build -f hotel-booking-backend/Dockerfile \
                    -t $ECR_REPO:backend-$BUILD_NUMBER .
                '''
            }
        }

        stage('Push to ECR') {
            steps {
                sh '''
                    aws ecr get-login-password --region $AWS_REGION \
                    | docker login --username AWS --password-stdin $ECR_REPO

                    docker push $ECR_REPO:frontend-$BUILD_NUMBER
                    docker push $ECR_REPO:backend-$BUILD_NUMBER
                '''
            }
        }
    }
}
