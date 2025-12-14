# 🚀 Node.js TODO App Deployment Using AWS ECS Fargate, ECR, and IAM

This repository contains a **Node.js TODO application** that is fully containerized using Docker and deployed on **AWS ECS with Fargate**. This README provides a **step-by-step guide** to deploy this application using **Amazon ECR**, **ECS Fargate**, and proper **IAM permissions**.

---

## 📌 Project Overview

**Repository:** `node-todo-app`  
**Tech Stack:**
- Node.js
- Express.js
- EJS (template engine)
- Docker
- AWS ECS Fargate
- Amazon ECR


---

## 🧩 Architecture Overview

```
EC2 Instance
   │
   ├── Docker build
   ├── AWS CLI push
   ▼
Amazon ECR (Public)
   │
   ▼
AWS ECS Fargate
   │
   ▼
Public Internet (HTTP)
```

---

## 📌 Prerequisites

- AWS Account
- Docker installed
- AWS CLI installed and configured
- Git installed
- Basic understanding of Docker & AWS

---

## 1. Install Docker give permissions and Download AWS CLI

```bash
sudo apt update && sudo apt upgrade
sudo apt install docker.io
sudo usermod -aG docker $USER
sudo reboot
```
Systeom reboot will apply this changes and connect EC2 again.
### Download AWS CLI 
```bash
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o awscliv2.zip
unzip awscliv2.zip
sudo ./aws/install
```
---

## 2.Configure IAM for AWS CLI (ECR Access)


### Create IAM User and Attach Poilicy 

1. Go to **IAM → Users** create new user
2. Attach the policy:
   - `AmazonElasticContainerRegistryPublicFullAccess`
    - `AmazonElasticContainerRegistryPublicPowerUser`
     - `AmazonElasticContainerRegistryPublicReadOnly`
3. Save the **Access Key ID** and **Secret Access Key**

### Configure AWS CLI

```bash
aws configure
```

---
## 3. Clone the Repository to ur EC2 Instance

```bash
git clone https://github.com/Mainul41561/node-todo-app.git
cd node-todo-app
```
## 2 Create Amazon ECR Public Repository

1. Go to **Amazon ECR → Public repositories → Create repository**
2. Repository name:
   ```
   node-todo
   ```
3. Create repository

### Authenticate Docker with ECR

```bash
aws ecr-public get-login-password --region us-east-1 \
| docker login --username AWS --password-stdin public.ecr.aws
```

---

## 4 Build and Push Docker Image

### Build Docker Image

```bash
docker build -t node-todo .
```

### Tag Docker Image

```bash
docker tag node-todo:latest public.ecr.aws/<registry-id>/node-todo:latest
```

### Push Image to ECR

```bash
docker push public.ecr.aws/<registry-id>/node-todo:latest
```

---

## 5 Create ECS Cluster (Fargate)

1. Go to **ECS → Clusters → Create Cluster**
2. Cluster name:
   ```
   node-todo
   ```
3. Infrastructure:
   - **AWS Fargate (serverless)**
4. Create cluster

---

## 6 Create ECS Task Definition (Fargate)

1. Go to **ECS → Task Definitions → Create new task definition**
2. Launch type:
   - **Fargate**
3. Task definition name:
   ```
   node-todo
   ```

### Task Size

- CPU: `0.25 vCPU`
- Memory: `0.5 GB`

### Container Configuration

- Container name: `node-todo`
- Image URI:
   ```
   public.ecr.aws/<registry-id>/node-todo:latest
   ```
- Container port: `8000`
- Protocol: TCP
- Essential container: Yes

### Execution Role

- Use or create:
  ```
  ecsTaskExecutionRole
  ```

Save the task definition.

---

## 7 Create ECS Service (Fargate)

1. ECS → Clusters → `node-todo`
2. Click **Create service**
3. Launch type:
   - Fargate
4. Task definition:
   - `node-todo`
5. Service name:
   ```
   node-todo
   ```
6. Desired tasks:
   - `1`

### Networking Configuration

- VPC: Default
- Subnets: Public subnets
- Auto-assign public IP: **Enabled**
- Security Group:
  - Allow inbound **TCP 8000** from `0.0.0.0/0`

Create the service.

---

## 8 Verify Deployment

1. ECS → Clusters → Services → Tasks
2. Open the running task
3. Copy the **Public IP**
4. Open in browser:

```bash
http://<TASK-PUBLIC-IP>:8000
```

🎉 Your **Node.js TODO App** is now live on **AWS ECS Fargate**.


## 📘 Summary

- Application is containerized using Docker
- Images stored in Amazon ECR
- Deployed using ECS Fargate (serverless)
- No EC2 or database required

---

## ✨ Author

**Mainul Alam**  
DevOps & Cloud Computing Enthusiast

---
