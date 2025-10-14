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
                // Set up Python virtual environment using Bash
                sh """
                    #!/bin/bash
                    python3 -m venv ${VENV_PATH}
                    . ${VENV_PATH}/bin/activate
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
                withEnv(["KUBECONFIG=${KUBECONFIG}"]) {
                    sh """
                        # Use the correct context name from your kubeconfig
                        CONTEXT_NAME=\$(kubectl --kubeconfig=$KUBECONFIG config get-contexts -o name | head -n 1)

                        kubectl --kubeconfig=$KUBECONFIG config use-context \$CONTEXT_NAME
                        kubectl config current-context
			kubectl apply -f k8s/deployment.yaml --context \$CONTEXT_NAME
			kubectl apply -f k8s/service.yaml --context \$CONTEXT_NAME
                        kubectl set image deployment/flask-app flask-app=${IMAGE_TAG} --context \$CONTEXT_NAME
			kubectl rollout status deployment/flask-app --context \$CONTEXT_NAME
                    """
                }
            }
        }
    }
}

