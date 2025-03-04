# Java Web Application - CI/CD Pipeline with Jenkins, Docker, and Kubernetes

## Overview

This repository contains a Java web application with a complete CI/CD pipeline implemented using Jenkins, Docker, and Kubernetes. The pipeline automates building, testing, and deploying the application across different environments (Development, QA, and Production).

## Features

- **Automated Build and Deployment** using Jenkins Pipeline
- **Containerized Application** using Docker
- **Deployment on Kubernetes** with Rolling Updates
- **Maven-based Build System**
- **Integration with Docker Hub for Image Storage**
- **Approval Step Before Production Deployment**

## Project Structure

```
.
├── Dockerfile
├── Jenkinsfile
├── k8s
│   ├── deployment.yaml
│   ├── service.yaml
├── pom.xml
└── src
```

## Technologies Used

- Java 8
- Apache Tomcat 8
- Maven
- Docker
- Kubernetes
- Jenkins

## Prerequisites

Ensure the following are installed before running the application:

- **JDK 8**
- **Apache Maven**
- **Docker**
- **Kubernetes Cluster**
- **Jenkins with Required Plugins**

## Build and Deployment Pipeline

### 1. Build Process

- Jenkins clones the repository
- Uses Maven to build the application and generate a WAR file

### 2. Dockerization

- Builds a Docker image using the generated WAR file
- Pushes the image to Docker Hub

### 3. Deployment Stages

- **Development Environment**: Deploys the containerized app locally
- **QA Environment**: Deploys the app on an EC2 instance
- **Production Environment**: Deploys the app on a Kubernetes cluster after approval

## Jenkins Pipeline Stages

The `Jenkinsfile` defines the following pipeline stages:

1. **SCM Checkout**: Clones the repository from GitHub
2. **Build using Maven**: Runs `mvn clean package`
3. **Build Docker Image**: Creates a Docker image from the WAR file
4. **Push to Docker Hub**: Uploads the image to Docker Hub
5. **Deploy to DEV**: Runs the container locally
6. **Deploy to QA**: Deploys the container on an EC2 instance
7. **QA Testing**: Runs a test using `curl`
8. **Approval Step**: Requires user confirmation before production deployment
9. **Deploy to Production**: Deploys the app on Kubernetes with scaling and a service

## Running the Application

### 1. Manual Build and Run

```sh
mvn clean package
```

```sh
docker build -t myapp:latest .
docker run -p 8080:8080 myapp:latest
```

### 2. Deploying to Kubernetes

Apply the Kubernetes YAML files:

```sh
kubectl apply -f k8s/deployment.yaml
kubectl apply -f k8s/service.yaml
```

Check the deployment:

```sh
kubectl get pods
kubectl get services
```

## Accessing the Application

- Development: `http://localhost:8080/java-web-app`
- QA: `http://<QA-EC2-Public-IP>:8080/java-web-app`
- Production: `http://<Production-IP>:8080/java-web-app`

## Docker Hub Repository

The Docker images are pushed to: `docker.io/ayush8410/javaweb`

## Kubernetes Configuration

### Deployment (`deployment.yaml`)

- Creates 3 replicas of the application
- Uses a rolling update strategy

### Service (`service.yaml`)

- Exposes the application using a `NodePort`
- Routes traffic to the running containers

## Contributing
If you’d like to contribute, please fork the repository and submit a pull request.

