pipeline {
    agent any

    environment {
        AWS_REGION = 'ap-south-1'
        REPO_NAME = 'ravi/1234'
        IMAGE_TAG = 'latest'
        AWS_ACCOUNT_ID = '178254122749'
        IMAGE_URI = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com/${REPO_NAME}:${IMAGE_TAG}"
    }

    stages {
        stage('Checkout Source Code') {
            steps {
                git url: 'https://github.com/Ravichandu-git/docker-sample-java-webapp1.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $IMAGE_URI .'
            }
        }

        stage('Login to ECR & Push Image') {
          steps {
                withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', credentialsId: 'aws-ecr-creds']]) {
                  sh '''
                        aws ecr get-login-password --region ap-south-1 | docker login --username AWS --password-stdin 178254122749.dkr.ecr.ap-south-1.amazonaws.com
                        docker push 178254122749.dkr.ecr.ap-south-1.amazonaws.com/ravi/1234:latest
                     '''
                }
             }
        }

        stage('Deploy to EKS via Helm') {
          steps {
                withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', credentialsId: 'aws-ecr-creds']]) {
                  sh '''
                        aws eks update-kubeconfig --name my-demo-cluster --region ap-south-1
                        helm upgrade --install java-webapp ./helm \
                        --set image.repository=178254122749.dkr.ecr.ap-south-1.amazonaws.com/ravi/1234 \
                        --set image.tag=latest
                  '''
                }
              }
        }  
    }
}
