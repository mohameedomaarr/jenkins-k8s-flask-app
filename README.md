
# Jenkins-K8s-Flask-App

**Owner:** Mohameed Omaarr  
**Repository Overview:** A sample Flask web application integrated with Jenkins for CI/CD and deployed on Kubernetes.  

---

## Repository Structure

- **app.py** – Main Flask application.  
- **Dockerfile** – Docker configuration to containerize the Flask app.  
- **.dockerignore** – Files and directories to exclude from the Docker build.  
- **Jenkinsfile** – Jenkins pipeline configuration for CI/CD.  
- **k8s/** – Kubernetes manifests and deployment configurations.  
- **templates/** – HTML templates used by the Flask app.  
- **requirements.txt** – Python dependencies for the Flask application.  
- **flask.service** – Systemd service file (if deploying locally).  
- **steps.sh** – Shell script with setup or deployment steps.  
- **tasks.txt** – Task list or instructions for the project.  
- **test_app.py** – Unit tests for the Flask app.  
- **acceptance-test.js** – Acceptance tests for the application.  

---

## Features

- Simple Flask web application.  
- Dockerized for easy deployment.  
- CI/CD pipeline configured with Jenkins.  
- Deployment-ready manifests for Kubernetes.  
- Includes unit and acceptance tests.  

---
