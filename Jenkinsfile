pipeline {
    agent any

    environment {
        IMAGE_NAME = 'mohameedomaarr/jenkins-k8s-flask-app'
        IMAGE_TAG = "${IMAGE_NAME}:${env.GIT_COMMIT}"
        KUBECONFIG = credentials('kubeconfig-credentials-id')
        VENV_PATH = "${WORKSPACE}/venv"
    }

    stages {
        stage('Setup') {
            steps {
                // Set up Python virtual environment
                sh """
                    python3 -m venv ${VENV_PATH}
                    source ${VENV_PATH}/bin/activate
                    pip install --upgrade pip
                    pip install -r requirements.txt
                """
                sh 'ls -la $KUBECONFIG'
                sh 'chmod 644 $KUBECONFIG'
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
                sh 'kubectl config use-context user@prod.us-east-1.eksctl.io'
                sh 'kubectl config current-context'
                sh "kubectl set image deployment/flask-app flask-app=${IMAGE_TAG}"
            }
        }
    }
}

