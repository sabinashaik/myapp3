pipeline {
    agent any

    environment {
        AWS_REGION = 'us-east-1'
        ECR_REPO = 'myapp3'
        IMAGE_TAG = "${BUILD_NUMBER}"
    }

    tools {
        maven 'MAVEN3'
        jdk 'JDK21'
    }

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/sabinashaik/myapp3.git'
            }
        }

        stage('Maven Build') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('SonarQube Scan') {
            steps {
                withSonarQubeEnv('SonarServer') {
                    sh 'mvn sonar:sonar'
                }
            }
        }

        stage('Upload Artifact to Nexus') {
            steps {
                sh 'mvn deploy'
            }
        }

        stage('Docker Build') {
            steps {
                sh 'docker build -t myapp3:1 .'
            }
        }

        stage('Push to ECR') {
            steps {
                sh '''
                aws ecr get-login-password --region us-east-1 | \
                docker login --username AWS --password-stdin xxxxxx.dkr.ecr.$AWS_REGION.amazonaws.com
                docker tag $ECR_REPO:$IMAGE_TAG 595658222114.dkr.ecr.us-east-1.amazonaws.com/myapp3:1
                docker push 595658222114.dkr.ecr.us-east-1.amazonaws.com/myapp3:1
                '''
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh '''
                helm upgrade --install myapp3 ./helm-chart \
                --set image.tag=$IMAGE_TAG \
                --namespace dev
                '''
            }
        }
    }

    post {
        failure {
            mail to: 'sabinashaik228@gmail.com',
            subject: "Build Failed: ${BUILD_NUMBER}",
            body: "Please check Jenkins logs."
        }
    }
}
