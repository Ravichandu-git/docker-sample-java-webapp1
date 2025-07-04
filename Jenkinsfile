pipeline {
    agent any

    environment {
        AWS_REGION = 'ap-south-1'
        REPO_NAME = 'java-webapp'
        IMAGE_TAG = 'latest'
        AWS_ACCOUNT_ID = '039483717602'
        IMAGE_URI = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com/${REPO_NAME}:${IMAGE_TAG}"
    }

    stages {
        stage('Checkout Source Code') {
            steps {
                git url: 'https://github.com/bhagyashreep032/docker-sample-java-webapp.git'
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
                        aws ecr get-login-password --region ap-south-1 | docker login --username AWS --password-stdin 039483717602.dkr.ecr.ap-south-1.amazonaws.com
                        docker push 039483717602.dkr.ecr.ap-south-1.amazonaws.com/java-webapp:latest
                     '''
                }
             }
        }


        stage('Deploy to EKS via Helm') {
            steps {
                sh '''
                    helm upgrade --install java-webapp ./helm \
                    --set image.repository=$AWS_ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com/$REPO_NAME \
                    --set image.tag=$IMAGE_TAG
                '''
            }
        }
    }
}
