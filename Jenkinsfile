pipeline {
    agent any

    environment {
        AWS_REGION = 'ap-south-2'
        ECR_REPO = '546517268801.dkr.ecr.ap-south-2.amazonaws.com/hotel-booking'
        VITE_API_BASE_URL = 'http://16.113.20.120:5001'
        APP_DIR = '/home/ubuntu/hotel-booking-devops/Hotel-Booking-Management-System--React-MERN-FullStack'
        KUBECONFIG = '/var/lib/jenkins/.kube/config'
        MINIKUBE_HOME = '/home/ubuntu/.minikube'
        DOCKER_CONFIG = '/var/lib/jenkins/.docker'
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

        stage('Deploy') {
            steps {
                sh '''
                    cd $APP_DIR
                    git pull origin main
                    IMAGE_TAG=$BUILD_NUMBER docker compose pull
                    IMAGE_TAG=$BUILD_NUMBER docker compose up -d
                '''
            }
        }

        stage('Health Check') {
            steps {
                sh '''
                    sleep 15
                    curl -f http://localhost:5001/api/health
                '''
            }
        }

        stage('Deploy Backend to Kubernetes') {
            steps {
                sh '''
                    echo "Loading backend image into Minikube"
                    minikube image load $ECR_REPO:backend-$BUILD_NUMBER

                    echo "Updating Kubernetes backend"
                    kubectl set image deployment/hotel-backend \
                    backend=$ECR_REPO:backend-$BUILD_NUMBER

                    kubectl rollout status deployment/hotel-backend --timeout=180s
                '''
            }
        }
    }
}
