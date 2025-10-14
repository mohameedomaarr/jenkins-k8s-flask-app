pipeline {
    agent any

    environment {
        IMAGE_NAME = 'mohameedomaarr/jenkins-k8s-flask-app'
        IMAGE_TAG = "${IMAGE_NAME}:${env.GIT_COMMIT}"
    }

    stages {

        stage('Setup') {
            steps {
                sh "pip install -r requirements.txt"
            }
        }

        stage('Login to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker-creds', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                    sh 'echo ${PASSWORD} | docker login -u ${USERNAME} --password-stdin'
                }
                echo 'Login successfully'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t ${IMAGE_TAG} .'
                echo "Docker image built successfully"
                sh 'docker image ls'
            }
        }

        stage('Push Docker Image') {
            steps {
                sh 'docker push ${IMAGE_TAG}'
                echo "Docker image pushed successfully"
            }
        }

        stage('Deploy to Prod') {
            steps {
                // Use IAM Role to authenticate AWS CLI automatically
                sh 'aws eks --region us-east-1 update-kubeconfig --name prod'
                sh 'kubectl config use-context arn:aws:eks:us-east-1:527111402169:cluster/prod'
                sh "kubectl set image deployment/flask-app flask-app=${IMAGE_TAG}"
            }
        }
    }
}

