FastAPI Hello World – Docker & Kubernetes Deployment


A simple FastAPI web application deployed via Docker and Kubernetes (Minikube)



Features
REST API on port 8000
/status endpoint returns a string "OK"
Dockerized with production-ready setup
Kubernetes manifests: Deployment + Service
Supports resource limits, replicas


1. Local Setup

Prerequisites
Docker installed
Python 3.10+
Minikube + kubectl
Git (optional)

2. Project structure
app.py
Dockerfile
requirements.txt
deployment.yaml
service.yaml
README.md

Run Locally Without Docker (Optional)
pip install -r requirements.txt
uvicorn app:app --reload --port 8000

Visit: http://localhost:8000/status

2. Dockerize the App
DOCKERFILE
FROM python:3.11-slim
WORKDIR /app

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY . .

EXPOSE 8000
CMD ["uvicorn", "app:app", "--host", "0.0.0.0", "--port", "8000"]


requirements.txt
fastapi
uvicorn


Build & Run Locally to test
docker build -t hello-fastapi:secure .
docker run -p 8000:8000 hello-fastapi:secure

run this command to scan for vulnerabilities before pushing to dockerhub registry
-trivy image hello-fastapi:secure


Go to: http://localhost:8000/status


2. Build and Push Docker Image to Docker Hub

docker login -  write your username and password 

docker build -t your-dockerhub-username/hello-fastapi:secure .
docker push your-dockerhub-username/hello-fastapi:secure

Replace your-dockerhub-username with your actual Docker Hub ID


deployment.yaml file
apiVersion: apps/v1
kind: Deployment
metadata:
  name: hello-app
spec:
  replicas: 2
  selector:
    matchLabels:
      app: hello-app
  template:
    metadata:
      labels:
        app: hello-app
    spec:
      containers:
      - name: hello-container
        image: macdee3030/hello-fastapi:secure
        ports:
        - containerPort: 8000
        resources:
          requests:
            cpu: "100m"
            memory: "128Mi"
          limits:
            cpu: "200m"
            memory: "256Mi"


service.yaml file
apiVersion: v1
kind: Service
metadata:
  name: hello-service
spec:
  type: NodePort
  selector:
    app: hello-app
  ports:
    - protocol: TCP
      port: 8000
      targetPort: 8000

Deploy It
kubectl apply -f deployment.yaml
kubectl apply -f service.yaml


check  running pods
kubectl get pods

to run the app locally on port 8000
-kubectl port-forward service/hello-service 8000:8000 





