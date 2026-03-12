pipeline {

    agent any

    environment {
        AWS_REGION = 'us-east-1'
        ECR_REPO = 'python-devops-app'
        ACCOUNT_ID = '123456789012'
        IMAGE_TAG = "${BUILD_NUMBER}"
        ECR_URL = "${ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com"
    }

    stages {

        stage('Checkout Code') {
            steps {
                git 'https://github.com/your-repo/python-devops-app.git'
            }
        }

        stage('Install Python Dependencies') {
            steps {
                sh '''
                python3 -m venv venv
                . venv/bin/activate
                pip install -r requirements.txt
                '''
            }
        }

        stage('Run Tests') {
            steps {
                sh '''
                echo "Run tests here"
                '''
            }
        }

        stage('Build Docker Image') {
            steps {
                sh '''
                docker build -t $ECR_REPO:$IMAGE_TAG .
                '''
            }
        }

        stage('Trivy Image Scan') {
            steps {
                sh '''
                trivy image $ECR_REPO:$IMAGE_TAG
                '''
            }
        }

        stage('Login to AWS ECR') {
            steps {
                sh '''
                aws ecr get-login-password --region $AWS_REGION | \
                docker login --username AWS --password-stdin $ECR_URL
                '''
            }
        }

        stage('Tag Docker Image') {
            steps {
                sh '''
                docker tag $ECR_REPO:$IMAGE_TAG \
                $ECR_URL/$ECR_REPO:$IMAGE_TAG
                '''
            }
        }

        stage('Push to ECR') {
            steps {
                sh '''
                docker push $ECR_URL/$ECR_REPO:$IMAGE_TAG
                '''
            }
        }

        stage('Deploy to Kubernetes using Helm') {
            steps {
                sh '''
                helm upgrade --install python-app ./helm/python-app \
                --set image.repository=$ECR_URL/$ECR_REPO \
                --set image.tag=$IMAGE_TAG
                '''
            }
        }

    }

}
