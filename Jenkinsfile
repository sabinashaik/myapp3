pipeline {
    agent any

    environment {
        AWS_REGION = 'us-east-1'
        ACCOUNT_ID = '123456789012'
        ECR_REPO = 'myapp3'
        IMAGE_TAG = "${BUILD_NUMBER}"
    }

    tools {
        maven "MAVEN3"
        jdk "JDK21"
    }

    stages {

      
        stage('Build WAR') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Docker Build') {
            steps {
                sh '''
                docker build -t $ECR_REPO:$IMAGE_TAG .
                '''
            }
        }

        stage('Login to ECR') {
            steps {
                sh '''
                aws ecr get-login-password --region $AWS_REGION | \
                docker login --username AWS --password-stdin $ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com
                '''
            }
        }

        stage('Tag & Push Image') {
            steps {
                sh '''
                docker tag $ECR_REPO:$IMAGE_TAG \
                $ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com/$ECR_REPO:$IMAGE_TAG

                docker push \
                $ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com/$ECR_REPO:$IMAGE_TAG
                '''
            }
        }
    }
}
