pipeline {
    agent any

    environment {
        IMAGE_NAME = 'mohameedomaarr/jenkins-k8s-flask-app'
        IMAGE_TAG = "${IMAGE_NAME}:${env.GIT_COMMIT}"
        KUBECONFIG = credentials('kubeconfig-credentials-id')
        AWS_ACCESS_KEY_ID = credentials('aws-access-key')
        AWS_SECRET_ACCESS_KEY = credentials('aws-secret-key')
        
    }

    
    stages {
        stage('Setup') {
            steps {
                sh 'ls -la $KUBECONFIG'
                sh 'chmod 644 $KUBECONFIG'
                sh 'ls -la $KUBECONFIG'
                sh "pip install -r requirements.txt"
            }
        }

        stage('Login to docker hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker-creds', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                sh 'echo ${PASSWORD} | docker login -u ${USERNAME} --password-stdin'}
                echo 'Login successfully'
            }
        }

        stage('Build Docker Image')
        {
            steps
            {
                sh 'docker build -t ${IMAGE_TAG} .'
                echo "Docker image build successfully"
                sh 'docker image ls'
                
            }
        }

        stage('Push Docker Image')
        {
            steps
            {
                sh 'docker push ${IMAGE_TAG}'
                echo "Docker image push successfully"
            }
        }

        stage('Deploy to Prod')
        {
            steps {
                sh 'kubectl config use-context user@prod.us-east-1.eksctl.io'
                sh 'kubectl config current-context'
                sh "kubectl set image deployment/flask-app flask-app=${IMAGE_TAG}"
            }
        }       

        
    }
}
