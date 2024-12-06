#  MERN Stack Application Deployment

# Overview
This project demonstrates the deployment of a MERN stack application (Frontend and Backend) on AWS EC2 instances using Minikube for Kubernetes and Jenkins for CI/CD automation. The application components are containerized using Docker and deployed in a Kubernetes cluster managed by Minikube. The pipeline is automated using Jenkins for seamless building, pushing, and deployment.

# Features
- Automated CI/CD pipeline using Jenkins.
- Deployment of frontend and backend components in Kubernetes with Helm charts.
- Scalable and modular architecture for modern web applications.
- Secure deployment on AWS EC2 instances.

# Prerequisites
AWS EC2 instances (t3.medium or higher) with Linux OS (Ubuntu 20.04 or later).
Docker installed and configured.
Minikube installed for Kubernetes cluster management.
Jenkins installed and accessible via port 8080.
MongoDB running and configured for the backend.

# Architecture
1. **Frontend**: React-based application.
2. **Backend**: Node.js with Express.
3. **Database**: MongoDB

Deployment Steps
1. Set Up EC2 Instances
Create AWS EC2 instances with t3.medium specifications.
Run the following command to update the system:
```
sudo apt update
```
3. Install Docker
Install Docker on your EC2 instances:
```sudo apt-get install -y docker.io
docker --version
```

3. Clone the Application Repositories
Clone the provided repositories to fetch the frontend and backend codebases:
```
git clone https://github.com/UnpredictablePrashant/learnerReportCS_frontend.git
git clone https://github.com/UnpredictablePrashant/learnerReportCS_backend.git
```
4. Build and Run Docker Images
Frontend:
Navigate to the frontend directory:
```
cd learnerReportCS_frontend
```
Update the Dockerfile:
Replace node:16 with node:18 due to version compatibility issues.

Build the Docker image:
```
docker build -t learner-frontend:latest .
```
Backend:
Navigate to the backend directory:
```
cd ../learnerReportCS_backend
```
Update the Dockerfile:
Replace node:16 with node:18.
Update the config.env file:
Ensure the correct MongoDB URI is provided.
Build the Docker image:

```docker build -t backend:latest .```
Run the Docker Containers:
Run the frontend container:

```
docker run -d -p 3000:3000 learner-frontend:latest
```
Run the backend container:
```docker run -d -p 5000:5000 backend:latest```

5. Push Docker Images to Docker Hub
Log in to Docker Hub:
```
sudo docker login
```
Tag and push the images:
Frontend:
```docker tag learner-frontend:latest pandamanish/learner-frontend:latest
docker push pandamanish/learner-frontend:latest
```
Backend:
```docker tag backend:latest pandamanish/backend:latest
docker push pandamanish/backend:latest
```
6. Install Minikube
Download and install Minikube:
```
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube
```
Start Minikube:
```
minikube start --driver=docker
```
Configure Docker to use Minikube’s environment:
```
eval $(minikube docker-env)
```
7. Deploy Using Kubernetes
Prepare Deployment Files:
Create frontend-deployment.yaml and backend-deployment.yaml files with appropriate configurations.
Ensure the correct Docker image name is specified.
Maintain proper indentation to avoid deployment errors.
Apply the Deployment Files:
Apply the frontend deployment:
```
kubectl apply -f frontend-deployment.yaml
```
Apply the backend deployment:
```
kubectl apply -f backend-deployment.yaml
```
Verify Deployment:
Check the running pods:
```
kubectl get pods
```
Check the services:
```
kubectl get services
```
Access the Applications:
Get the Minikube IP:
```
minikube ip
```
Use the Minikube IP with the respective NodePorts to access the applications:
```
Frontend: http://192.168.49.2:30080
Backend: http://192.168.49.2:30081
```
8. Create and Deploy Helm Charts
Frontend:
Create a Helm chart:
```
helm create frontend
```
Update the values.yaml and templates (deployment.yaml, service.yaml) with values from frontend-deployment.yaml.
Install the frontend Helm chart:
```
helm install frontend ./frontend
```

Backend:
Create a Helm chart:
```
helm create backend
```
Update the values.yaml and templates (deployment.yaml, service.yaml) with values from backend-deployment.yaml.

Install the backend Helm chart:

```
helm install backend ./backend
```
Upgrade if necessary:

```
helm upgrade backend ./backend
```
9. Set Up Jenkins for CI/CD
Once installed, Jenkins was accessed at http://<instance-IP>:8080. After unlocking Jenkins using the initial admin password and completing the setup wizard, we installed the necessary plugins:

Pipeline
Docker Pipeline
GitHub
Kubernetes
Install Jenkins:
Install Jenkins on your EC2 instance:

```
sudo apt update
sudo apt install -y openjdk-11-jdk
wget -q -O - https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo apt-key add -
sudo sh -c 'echo deb http://pkg.jenkins.io/debian-stable binary/ > /etc/apt/sources.list.d/jenkins.list'
sudo apt update
sudo apt install -y jenkins
sudo systemctl start jenkins
sudo systemctl enable jenkins
```
Access Jenkins on http://<instance-IP>:8080.

Install Plugins:
Install the following plugins:
Pipeline
Docker Pipeline
GitHub
Kubernetes
Add Docker Hub Credentials:
Go to Manage Jenkins → Manage Credentials.
Add a new credential with:
Username: Your Docker Hub username.
Password: Your Docker Hub password.
ID: docker-hub-credentials.
Set Up Jenkinsfile:
Add a Jenkinsfile to each repository (frontend and backend) with Groovy code for CI/CD automation.

Push the Jenkinsfile to GitHub:

```
git add Jenkinsfile
git commit -m "Added Jenkinsfile for CI/CD"
git push origin main
```
Create Jenkins Pipeline Jobs:

Go to Jenkins Dashboard → New Item.
Name the pipeline (frontend-pipeline or backend-pipeline).
Under the Pipeline section:
Select "Pipeline script from SCM."
Choose Git as SCM.
Provide the repository URL and set the script path to Jenkinsfile.
Set the script path to Jenkinsfile.
Save the configuration.

Run the Pipeline:
Click Build Now to trigger the pipeline. 

















