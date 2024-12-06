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

# Step
1.created EC2 instances with t3.medium post which we ran
sudo apt update
2.Install Docker using the following commands:
```
sudo apt-get install -y docker.io
docker --version
```
3.Did git clone to the repository mentioned above to get the frontend and backend folder.
```cd learnerReportCS_frontend
```
**Note**- update the Dockerfile in that repo from Node :16 to 18, as it was getting error based on version compatibility.
Once the Dockerfile is ready , run the docker build for frontend
```
docker build -t learner-frontend:latest .
```
```cd ../learnerReportCS_backend
```
**Note**- did the exact same thing for backend , updated the dockerfile with version 18 and surely change your mongo uri in the config.env file. 

```docker build -t backend:latest .
```
once the build is done ,run it with below mentioned ports:
```
docker run -d -p 3000:3000 learner-frontend:latest
docker run -d -p 5000:5000 backend:latest
```
post that when you check you might get error with after tag and pushing the image to docker hub for which you need to run 
```sudo docker login ``` - mentioned the passsword and username and login should get successful.

now , tagged the image:
```docker tag learner-frontend:latest pandamanish/learner-frontend:latest
```
```docker tag backend:latest pandamanish/backend:latest
```
and psuhed to docker hub:
```docker push pandamanish/learner-frontend:latest
  docker push pandamanish/backend:latest
```
Next step was to Instal minikube :
```curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube
```
once the image is pushed to docker , now created a frontend-deployment.yaml and backend-deployment.yaml files specifically keep check of image name nad indentation, as i faced error with indentation in the learner-frontend & learner-backend foler.
image of yaml files here:

check if kubectl is installed or not:
```kubectl version --client```
if not installed run these below commands to install kubectl:

define each:
```curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"```
```chmod +x kubectl```
```sudo mv kubectl /usr/local/bin/```
now once everything is installed , can start the minikube:
```minikube start --driver=docker
```
```
eval $(minikube docker-env)
```
Once the deployment files are ready can run the apply for the kubectl on each deployment file:
```
kubectl apply -f frontend-deployment.yaml
```
```
kubectl apply -f backend-deployment.yaml
```
now to check the pods are running perfectly:
```
kubectl get pods
```
and the internal and external services:
```
kubectl get services
```
once the pods and services are running perfectly , can run ```minikube ip``` to get the ip and with that port no which we have mentioned in the external service 
kubectl get services

Access the applications:

Frontend: http://<Minikube-IP>:30080
Backend: http://<Minikube-IP>:30081

Next for creating the HELM chart:
helm create frontend
created values.yaml file will store the customizable values for the Helm chart from the frontend-deployment.yaml
image of file:
To replace static deployment YAML with a templated version. Update templates/deployment.yaml
image of file:
Created service.yaml and moved Service definition from frontend-deployment.yaml into a separate file called templates/service.yaml
created chart.yaml to mention the metadata of heml chart


cd learnerReportCS_frontend/helm/frontend

helm install frontend ./frontend
kubectl get pods
kubectl get services]]]

helm upgrade frontend ./frontend

helm create backend
values.yaml File for Backend Update the values.yaml file for the backend service:
Update the templates/deployment.yaml Replace the static backend deployment YAML with a templated version in templates/deployment.yaml:
Update the templates/service.yaml Replace the static backend service YAML with a templated version in templates/service.yaml
Verify Chart.yaml Ensure the Chart.yaml file contains appropriate metadata for the backend
Deploy the Backend Helm Chart

helm install backend ./backend
kubectl get pods
kubectl get services

Now jekins part:

Install:

sudo apt update
sudo apt install -y openjdk-11-jdk
wget -q -O - https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo apt-key add -
sudo sh -c 'echo deb http://pkg.jenkins.io/debian-stable binary/ > /etc/apt/sources.list.d/jenkins.list'
sudo apt update
sudo apt install -y jenkins
sudo systemctl start jenkins
sudo systemctl enable jenkinsInstall Required Plugins:

Install the following plugins from the Jenkins dashboard:
Pipeline
Docker Pipeline
GitHub
Kubernetes

Go to Manage Jenkins → Manage Credentials.
Add a new credential with the following:
Username: Your Docker Hub username.
Password: Your Docker Hub password.
ID: docker-hub-credentials.

Created jenkins file /groovy code

git add Jenkinsfile
git commit -m "Added Jenkinsfile for CI/CD"
git push origin main


Set Up Jenkins Pipeline Jobs:

Go to Jenkins Dashboard → New Item.
Select Pipeline, and name it frontend-pipeline or backend-pipeline.
Under the Pipeline section:
Choose "Pipeline script from SCM."
Select Git as SCM.
Provide the repository URL (e.g., https://github.com/UnpredictablePrashant/learnerReportCS_frontend.git).
Set the script path to Jenkinsfile.

Trigger the Pipeline:

Save the pipeline configuration.
Click Build Now to run the pipeline.

















