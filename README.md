# 🚀 EasyCRUD – Cloud-Native Student Registration System on AWS (EKS + RDS + Docker)
EasyCRUD is a full-stack CRUD web application deployed using modern **DevOps** and **cloud-native technologies**.  
This project demonstrates **containerization with Docker**, **Kubernetes orchestration on AWS EKS**, **managed database integration with Amazon RDS**, and a **scalable microservices architecture**.


### Deployed Using

- Docker
- AWS EKS (Kubernetes)
- AWS RDS (MariaDB)
- Kubernetes Services
- Load Balancer / Ingress
## 🎯 Key Highlights

- ✅ End-to-end cloud deployment on AWS  
- ✅ Dockerized frontend and backend services  
- ✅ Kubernetes orchestration using Amazon EKS  
- ✅ Secure MariaDB database hosted on Amazon RDS  
- ✅ Scalable microservices architecture  
- ✅ Production-style DevOps workflow  
## 🛠️ Technologies Used

- **Frontend:** React  
- **Backend:** Spring Boot  
- **Database:** Amazon RDS (MariaDB)  
- **Containers:** Docker & Docker Hub  
- **Orchestration:** Kubernetes (AWS EKS)  
- **Cloud Platform:** AWS EC2, EKS, RDS  
## 📂 This Repository Contains

- ✔ Database setup on **AWS RDS**
- ✔ Backend Dockerization and deployment
- ✔ Frontend Dockerization and deployment
- ✔ Kubernetes pod and service configurations
- ✔ Complete production-ready DevOps workflow

## 🟢 PHASE 1: Database Setup (Amazon RDS – MariaDB)

📘 Follow the complete database setup guide (**Step 1 to Step 6**) here:  
👉 [Database Setup Documentation](https://github.com/pranavmisal1002/EasyCrud-Docker#-phase-1-database-setup-amazon-rds--mariadb)

## 🟢 PHASE 2: Create AWS EKS Cluster

📘 Follow the complete AWS EKS cluster setup guide here:  
👉 [AWS EKS Setup Documentation](https://github.com/pranavmisal1002/AWS-EKS-Setup)

## 🔵 Backend Deployment Steps (Docker + DockerHub + EKS)

## Step 1: Install Docker

Install Docker on the EC2 instance and start the Docker service.

```bash
sudo apt install docker.io -y
sudo systemctl start docker
```

## Step 2: Clone Project Repository

Clone the EasyCRUD project repository and navigate to the backend directory.

```bash
git clone https://github.com/Rohit-1920/EasyCRUD.git
```
Move to backend directory
```bash
cd EasyCRUD/backend/
```

## Step 3: Configure Backend Application

Copy the `application.properties` file to the backend root directory and edit it.

```bash
cp src/main/resources/application.properties .
```
```bash
nano application.properties
```
### Update Configuration Values

Update the following values in the `application.properties` file:

- **RDS Endpoint**
- **Database Name:** `student_db`
- **Database Username**
- **Database Password**

```bash
server.port=8080
spring.datasource.url=jdbc:mariadb://<RDS-EndPoint>:3306/student_db
spring.datasource.username=<username>
spring.datasource.password=<password>
spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true
```

## Step 4: Create Dockerfile for Backend

Create a Dockerfile for the backend application.

```bash
nano Dockerfile
```
Add the following content to the Dockerfile:

```bash
FROM maven:3.8.5-openjdk-17
COPY . /opt/
WORKDIR /opt
RUN rm -rf src/main/resources/application.properties
RUN cp -r application.properties src/main/resources/
RUN mvn clean package
WORKDIR target/
CMD ["java","-jar","student-registration-backend-0.0.1-SNAPSHOT.jar"]
```
## Step 5: Build Backend Docker Image

Build the Docker image for the backend application and verify the image creation.
```bash
docker build -t <images-name>:<tag> <dockerfile-path>
```
Example :
```bash
docker build -t backend:v1 .
```
Verify Docker images
```bash
docker images
```
### Step 6: Run Backend Container Locally (Testing)

Run the backend Docker container on port `8080`:

```bash
docker run -d -p 8080:8080 backend:v1
```
Check running containers:
```bash
docker ps
```
✅ Verify backend in browser:
```bash
http://<BACKEND_EC2_PUBLIC_IP>:8080
```
> ✅ **Note:** Once the Docker image is built, rename (tag) it using the `docker tag` command before pushing it to Docker Hub.
### Tag the Docker Image for Docker Hub
**Syntax:**

```bash
docker tag <LOCAL_IMAGE_NAME>:<TAG> <DOCKERHUB_USERNAME>/<REPOSITORY_NAME>:<TAG>
```
**Example:**

```bash
docker tag backend:v1 pranavmisal1002/backend:v1
```
### Step 7: Push Backend Image to Docker Hub

Login to Docker Hub:

```bash
docker login -u <username>
```
Push the backend image to Docker Hub repository:
```bash
docker push pranavmisal1002/backend:v1
```
✅ Backend image is now available for deployment on EKS.

## ✅ Deploy Backend on EKS Kubernetes Cluster
### Step 8: Login to EKS Master / Bastion Node

Login to the EC2 instance where `kubectl` is configured (EKS access node).

Verify EKS cluster connectivity:

```bash
kubectl get nodes
```
### Step 9: Create Backend Pod YAML

Create a Kubernetes manifest file for the backend pod and Service:

```bash
nano deployment.yml
```
### 📄 Backend Deployment & Service Manifest (`pod.yml`)

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: backend-dep
spec:
  replicas: 1
  selector:
    matchLabels:
      app: backend
  template:
    metadata:
      name: backend-pod
      labels:
        app: backend
    spec:
      containers:
        - name: backend
          image: pranavmisal1002/backend2:v1
          ports:
            - containerPort: 8080
---
apiVersion: v1
kind: Service
metadata:
  name: backend-svc
spec:
  type: LoadBalancer
  selector:
    app: backend
  ports:
    - name: lb
      protocol: TCP
      port: 8080
      targetPort: 8080
```
### Step 10: Deploy Backend Pod and Service

Create the backend pod and service:

```bash
kubectl apply -f deployment.yml
```
Verify that the pod is running:
```bash
kubectl get pods -o wide
```
Verify that the service is created:
```bash
kubectl get svc
```
### Step 11: Verify Backend on EKS

Check the backend service external IP (LoadBalancer):

```bash
kubectl get svc backend-service
```
Once the EXTERNAL-IP is available, open in your browser:
```bash
http://<LOADBALANCER_EXTERNAL_IP>:8080
```
🎉 Backend pod setup on EKS is complete!
