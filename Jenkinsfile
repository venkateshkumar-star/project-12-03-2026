pipeline {

    agent any

    environment {
        AWS_REGION = 'ap-south-1'
        ECR_REPO = 'python-devops-app'
        ACCOUNT_ID = '030926982328'
        IMAGE_TAG = "${BUILD_NUMBER}"
        ECR_URL = "${ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com"
    }

    pipeline {
    agent any

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/venkateshkumar-star/project-12-03-2026.git'
            }
        }

        stage('Build') {
            steps {
                sh 'echo Build Stage'
            }
        }

        stage('Test') {
            steps {
                sh 'echo Running Tests'
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
