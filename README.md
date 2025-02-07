**CI/CD for Dockerized Applications on Kubernetes Kind Cluster**

**About This Project**
This project demonstrates the automation of a CI/CD pipeline for a Dockerized Node.js application using Jenkins and Kubernetes (Kind Cluster). It follows DevOps best practices by integrating:

 Continuous Integration (CI): Automates the build and testing process to ensure code quality.
 Continuous Deployment (CD): Deploys the application to a Kubernetes Kind cluster with minimal manual intervention.
 Dockerization: Packages the application into a container for consistent deployment across different environments.
 Kubernetes Deployment: Ensures scalability and efficient resource management through container orchestration.

**Why This Project?**
In modern DevOps workflows, automation plays a crucial role in enhancing efficiency, reliability, and scalability. This project is designed to bridge the gap between development and operations teams, ensuring a seamless deployment process while reducing manual effort.

 Efficient and Reliable Deployments: Each code change is automatically built, tested, and deployed, reducing errors and improving release cycles.
 Scalability with Kubernetes: The application runs within a local Kubernetes Kind cluster, replicating real-world production environments.
 Effective Image Management: The Dockerized application is stored in Docker Hub, ensuring centralized and version-controlled image distribution.
By implementing this project, teams can achieve faster software delivery, better resource utilization, and a more reliable deployment process, making it a valuable addition to any DevOps workflow.

# CI/CD for Dockerized Applications on Kubernetes Kind Cluster
- Automate CI/CD using Jenkins
- Dockerized Application
- Pushed to Docker Hub
- Deployed to Kubernetes (Kind Cluster)

**📂 Project Overview**
This project sets up a Continuous Integration & Deployment (CI/CD) pipeline using Jenkins to:

Build & push a Dockerized Node.js app to Docker Hub.
Deploy the app on a Kubernetes Kind cluster using Jenkins.

🔹 Tech Stack Used:
Docker → Containerization
Jenkins → CI/CD Pipeline
Docker Hub → Image Storage
Kubernetes (Kind Cluster) → Deployment

**📂 Project Structure**

ci-cd-app/
│── Dockerfile
│── app.js
│── package.json
│── deployment.yaml
│── Jenkinsfile
│── README.md

**Step 1: Clone the Repository**
git clone https://github.com/github-username/ci-cd-app.git
cd ci-cd-app

**Step 2: Set Up a Kubernetes Kind Cluster**
Install Kind & Kubectl:

# Install Kind
curl -Lo ./kind https://kind.sigs.k8s.io/dl/latest/kind-linux-amd64
chmod +x ./kind
sudo mv ./kind /usr/local/bin/kind

# Install Kubectl
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
chmod +x kubectl
sudo mv kubectl /usr/local/bin/

# Create a Kubernetes Cluster:
kind create cluster --name devops-cluster
kubectl cluster-info --context kind-devops-cluster
kubectl get nodes

**Step 3: Create a Simple Node.js Application**
# Create "app.js" file and below script:

const express = require('express');
const app = express();

app.get('/', (req, res) => {
    res.send('Hello from Kubernetes CI/CD Pipeline!');
});

app.listen(3000, () => {
    console.log('App running on port 3000');
});

# Create "package.json" file and below data:

{
  "name": "ci-cd-app",
  "version": "1.0.0",
  "description": "A simple Node.js app for Kubernetes CI/CD",
  "main": "app.js",
  "scripts": {
    "start": "node app.js"
  },
  "dependencies": {
    "express": "^4.18.2"
  }
}

**Step 4: Dockerize the Application**
# Create a "Dockerfile":

FROM node:16
WORKDIR /app
COPY package.json .
RUN npm install
COPY . .
EXPOSE 3000
CMD ["node", "app.js"]

# Build & Push Docker Image:
docker login -u <your-dockerhub-username>
docker build -t <your-dockerhub-username>/ci-cd-app:latest .
docker push <your-dockerhub-username>/ci-cd-app:latest

(Replace <your-dockerhub-username> with your Docker Hub username.)

**Step 5: Deploy to Kubernetes**
# Create a "deployment.yaml"

apiVersion: apps/v1
kind: Deployment
metadata:
  name: ci-cd-app
spec:
  replicas: 1
  selector:
    matchLabels:
      app: ci-cd-app
  template:
    metadata:
      labels:
        app: ci-cd-app
    spec:
      containers:
        - name: ci-cd-app
          image: <your-dockerhub-username>/ci-cd-app:latest
          ports:
            - containerPort: 3000

# Create a "service.yaml" file:

apiVersion: v1
kind: Service
metadata:
  name: ci-cd-service
spec:
  selector:
    app: ci-cd-app
  ports:
    - protocol: TCP
      port: 80
      targetPort: 3000
  type: NodePort

# Apply the deployment and service:
kubectl apply -f deployment.yaml
kubectl apply -f service.yaml
kubectl get pods
kubectl get svc

**Step 6: Set Up CI/CD with Jenkins**
sudo apt update
sudo apt install openjdk-17-jdk -y
wget -q -O - https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo apt-key add -
sudo sh -c 'echo deb http://pkg.jenkins.io/debian-stable binary/ > /etc/apt/sources.list.d/jenkins.list'
sudo apt update
sudo apt install jenkins -y
sudo systemctl start jenkins
sudo systemctl enable jenkins

Access Jenkins → http://localhost:8080 & Follow offical jenkins website also to dowload the jenkins.

# Create Jenkinsfile

pipeline {
    agent any
    environment {
        DOCKER_HUB_CREDENTIALS = credentials('docker-hub')
    }
    stages {
        stage('Checkout Code') {
            steps {
                git 'https://github.com/github-username/ci-cd-app.git'
            }
        }
        stage('Build Docker Image') {
            steps {
                sh 'docker build -t <your-dockerhub-username>/ci-cd-app:latest .'
            }
        }
        stage('Push Docker Image') {
            steps {
                withDockerRegistry([credentialsId: 'docker-hub', url: '']) {
                    sh 'docker push <your-dockerhub-username>/ci-cd-app:latest'
                }
            }
        }
        stage('Deploy to Kubernetes') {
            steps {
                sh 'kubectl apply -f deployment.yaml'
            }
        }
    }
}

Replace <your-dockerhub-username> with your Docker Hub username.

# Configure Jenkins:

1) Install Plugins:
 - Docker Pipeline
 - Kubernetes CLI
 - 
2) Add Docker Hub Credentials:
- Go to Jenkins → Manage Jenkins → Credentials
- Add Docker Hub username & password
- Use ID: docker-hub
  
3) Run the Pipeline:
- Open Jenkins Dashboard
- Create New Pipeline → Select Pipeline from SCM
- Enter GitHub URL → https://github.com/sanketp66/ci-cd-app.git
- Save & Run Pipeline

**Step 7: Access the Application**
- kubectl get svc ci-cd-service

3 Find the NodePort, then access:
- http://localhost:<NodePort>

🎯 Conclusion
✅ Automated CI/CD Pipeline using Jenkins
✅ Dockerized Application pushed to Docker Hub
✅ Deployed to Kubernetes (Kind Cluster)

🚀 Project Successfully Deployed! 🚀














