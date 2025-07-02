pipeline {
    agent any
    environment {
        FRONTEND_IMAGE_NAME = "practical-devops-frontend"
        BACKEND_IMAGE_NAME = "practical-devops-backend"
        ECR_URI = "842273289390.dkr.ecr.us-east-1.amazonaws.com/sd0188-practical-devop"
        ECR_URI_FRONTEND = "842273289390.dkr.ecr.us-east-1.amazonaws.com/sd0188-practical-devop-frontend"
        ECR_URI_BACKEND = "842273289390.dkr.ecr.us-east-1.amazonaws.com/sd0188-practical-devop-backend"
        FRONTEND_TAG = "frontend-latest"
        BACKEND_TAG = "backend-latest"
        REGION = "us-east-1"
        EKS_CLUSTER_NAME = "sd0188-devop-for-dev"
    }

    stages {
        stage('Login to ECR') {
            steps {
                script {
                    echo "Logging in to ECR..."
                    withAWS(credentials: '4eb33416-0bea-400e-9a40-5320d9112428', region: 'us-east-1') {
                        sh "aws ecr get-login-password --region ${REGION} | docker login --username AWS --password-stdin ${ECR_URI}"
                    }
                }
            }
        }

        stage('Build Front End Image') {
            steps {
                echo "Building Frontend Image..."
                    sh "docker build -t ${FRONTEND_IMAGE_NAME}:latest src/frontend"
                    sh "docker tag ${FRONTEND_IMAGE_NAME}:latest ${ECR_URI_FRONTEND}:${FRONTEND_TAG}"
                    sh "docker push ${ECR_URI_FRONTEND}:${FRONTEND_TAG}"
            }
        }

        stage('Build Back End Image') {
            steps {
                echo "Building Backend Image..."
                    sh "docker build -t ${BACKEND_IMAGE_NAME}:latest src/backend"
                    sh "docker tag ${BACKEND_IMAGE_NAME}:latest ${ECR_URI_BACKEND}:${BACKEND_TAG}"
                    sh "docker push ${ECR_URI_BACKEND}:${BACKEND_TAG}"
            }
        }

        stage('Deploy k8s') {
            steps {
                withAWS(credentials: '4eb33416-0bea-400e-9a40-5320d9112428', region: 'us-east-1') {
                echo "Deploying to Kubernetes..."
                sh "aws eks update-kubeconfig --name ${EKS_CLUSTER_NAME} --region ${REGION}"
                sh "kubectl apply -f ./mongodb.yaml"
                sh "kubectl apply -f ./backend.yaml"
                sh "kubectl apply -f ./frontend.yaml"
                }
            }
        }
    }
}
