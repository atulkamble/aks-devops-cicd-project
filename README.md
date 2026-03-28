# 🚀 DevOps Project: AKS-Based CI/CD Deployment

## 📌 Project Name

**aks-devops-cicd-project**

---

```
dev stack - python flask 

python --version 
pip --version 

pip install -r requirements.txt 
python app.py 

http://localhost:5000 

// modern UI << AI 

AzureVM >>

cd Downloads 
sudo chmod 400 key.pem 
ssh -i key.pem atul@68.218.153.232

sudo apt update -y 
sudo apt list --upgradable
sudo apt upgrade -y 
sudo apt install git docker.io tree python3-pip python-is-python3 -y 
git --version 
docker --version 
tree --version 
python --version 
pip --version
git config --global user.name "Atul Kamble"
git config --global user.email "atul_kamble@hotmail.com"
git config --list 
sudo systemctl start docker 
sudo systemctl enable docker 
sudo systemctl status docker
sudo usermod -aG docker $USER
newgrp docker
sudo docker login -u atuljkamble
git clone https://github.com/atulkamble/aks-devops-cicd-project.git
cd aks-devops-cicd-project

sudo docker build -t docker.io/atuljkamble/python-flask-app:latest .
sudo docker push docker.io/atuljkamble/python-flask-app:latest
sudo docker images 
sudo docker run -d -p 5000:5000 atuljkamble/python-flask-app
sudo docker container ls


http://68.218.153.232:5000/

stage 3 >> k8s 

az aks create --resource-group dev --name aks-cluster --node-count 2 --enable-addons monitoring --generate-ssh-keys

az aks get-credentials --resource-group dev --name aks-cluster

cd k8s 

kubectl apply -f deployment.yaml 
kubectl apply -f service.yaml 

kubectl get nodes 
kubectl get pods 
kubectl get services 

```

## 🎯 Aim

To build a **production-ready CI/CD pipeline** that deploys a containerized application to **Azure Kubernetes Service (AKS)** using DevOps best practices.

---

## 🎯 Objectives

* Containerize application using Docker
* Push image to Azure Container Registry (ACR)
* Create AKS cluster
* Deploy app using Kubernetes manifests
* Implement CI/CD using GitHub Actions / Azure DevOps
* Monitor & scale application

---

## 🧰 Services Used

* Azure Kubernetes Service (AKS)
* Azure Container Registry (ACR)
* Azure CLI
* Docker
* GitHub Actions / Azure DevOps
* Kubernetes (kubectl)

---

## 📂 Project Structure

```
aks-devops-cicd-project/
│
├── app/
│   ├── app.py
│   ├── requirements.txt
│
├── Dockerfile
├── k8s/
│   ├── deployment.yaml
│   ├── service.yaml
│
├── .github/workflows/
│   └── deploy.yml
│
└── README.md
```

---

# 🧑‍💻 Step 1: Sample Python App

## 📄 `app/app.py`

```python
from flask import Flask
app = Flask(__name__)

@app.route('/')
def home():
    return "AKS DevOps Project Running 🚀"

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000)
```

## 📄 `app/requirements.txt`

```
flask
```

---

# 🐳 Step 2: Dockerfile

```dockerfile
FROM python:3.9-slim

WORKDIR /app
COPY app/ /app

RUN pip install -r requirements.txt

CMD ["python", "app.py"]
```

---

# ☁️ Step 3: Azure Setup

## 🔹 Login

```bash
az login
```

## 🔹 Create Resource Group

```bash
az group create --name aks-rg --location eastus
```

## 🔹 Create ACR

```bash
az acr create --resource-group aks-rg \
  --name myacrdevops123 \
  --sku Basic
```

## 🔹 Login to ACR

```bash
az acr login --name myacrdevops123
```

---

# 🐳 Step 4: Build & Push Image

```bash
docker build -t myacrdevops123.azurecr.io/aks-app:v1 .
docker push myacrdevops123.azurecr.io/aks-app:v1
```

---

# ⚙️ Step 5: Create AKS Cluster

```bash
az aks create \
  --resource-group aks-rg \
  --name aks-cluster \
  --node-count 2 \
  --enable-addons monitoring \
  --generate-ssh-keys \
  --attach-acr myacrdevops123
```

---

# 🔗 Step 6: Connect to AKS

```bash
az aks get-credentials \
  --resource-group aks-rg \
  --name aks-cluster
```

---

# ☸️ Step 7: Kubernetes Deployment

## 📄 `k8s/deployment.yaml`

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: aks-app
spec:
  replicas: 2
  selector:
    matchLabels:
      app: aks-app
  template:
    metadata:
      labels:
        app: aks-app
    spec:
      containers:
      - name: aks-app
        image: myacrdevops123.azurecr.io/aks-app:v1
        ports:
        - containerPort: 5000
```

---

## 📄 `k8s/service.yaml`

```yaml
apiVersion: v1
kind: Service
metadata:
  name: aks-service
spec:
  type: LoadBalancer
  selector:
    app: aks-app
  ports:
  - protocol: TCP
    port: 80
    targetPort: 5000
```

---

## 🚀 Deploy to AKS

```bash
kubectl apply -f k8s/
```

---

## 🌐 Get External IP

```bash
kubectl get svc aks-service
```

---

# 🔄 Step 8: CI/CD Pipeline (GitHub Actions)

## 📄 `.github/workflows/deploy.yml`

```yaml
name: AKS Deployment

on:
  push:
    branches:
      - main

jobs:
  build-deploy:
    runs-on: ubuntu-latest

    steps:
    - name: Checkout Code
      uses: actions/checkout@v3

    - name: Login to Azure
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}

    - name: Build & Push Docker Image
      run: |
        az acr login --name myacrdevops123
        docker build -t myacrdevops123.azurecr.io/aks-app:v1 .
        docker push myacrdevops123.azurecr.io/aks-app:v1

    - name: Set AKS Context
      run: |
        az aks get-credentials --resource-group aks-rg --name aks-cluster

    - name: Deploy to AKS
      run: |
        kubectl apply -f k8s/
```

---

# 📊 Step 9: Scaling & Monitoring

## 🔹 Scale Pods

```bash
kubectl scale deployment aks-app --replicas=5
```

## 🔹 Enable Autoscaling

```bash
kubectl autoscale deployment aks-app --cpu-percent=70 --min=2 --max=10
```

---

# 📈 Architecture Flow

```
Developer → GitHub → CI/CD Pipeline → ACR → AKS → LoadBalancer → Users
```

---

# 🧪 Bonus Enhancements (Production Level)

* 🔐 Use Kubernetes Secrets
* 📦 Helm Charts
* 🔄 Blue-Green Deployment
* 🔍 Azure Monitor + Log Analytics
* 🔁 GitOps using ArgoCD
* 🔐 Use Managed Identity instead of secrets

---

# 📘 Conclusion

This project demonstrates:

* End-to-end DevOps lifecycle
* Containerization + Kubernetes deployment
* CI/CD automation
* Real-world AKS usage

---
