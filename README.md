# Kubernetes Nodejs Project

## Dockerhub Repo

[![Docker][docker-shield]][docker-url]

### Run the container on localhost:3000

```
docker run -d -p 3000:3000 vishnusureshperumbavoor2/node-k8s-app
```

### Pull the image from Docker Hub

```
docker pull vishnusureshperumbavoor2/node-k8s-app
```

## Architecture

### Microservices

Each service is focused on a specific business function and can be developed, deployed, and scaled independently.

In traditional monolithic architectures, all components of an application are tightly integrated and deployed as a single unit.

## Technologies

### Docker

Platform to develop, ship, and run applications in containers

### Kubernetes

Container orchestration platform that automates the deployment, scaling, and management of containerized applications

### minikube

Used to run kubernetes cluster locally on the machine.
No need of full scale cluster.
Supports multiple hypervisors (VirtualBox, VMWare Fusion, HyperKit, KVM)

### kubectl

CLI for interacting with Kubernetes clusters. It allows to deploy applications, managing resources, inspecting cluster state, and debugging issues.

## Terminologies

### Container Orchestration

Kubernetes allows to orchestrate the containerized applications across a cluster of machines.

Makes it easy to scale applications horizontally by adding or removing container instances based on demand.

## Prerequisites

1. NodeJS
2. Docker
3. minikube (to use kubernetes cluster locally)
4. kubectl

Get docker hub access token from [docker hub security](https://hub.docker.com/settings/security)

Authenticate with your docker registry

```sh
docker login -u vishnusureshperumbavoor2
```

Set up Docker client to use the Docker daemon running inside the Minikube VM.

```sh
eval $(minikube docker-env)
```

## NodeJS + Docker + K8s tutorials

### Step 1: Create a Simple Node.js Application

Initialize a new Node.js project & install express

```sh
npm init -y
npm install express
```

### Create an index.js file with the following content:

```sh
const express = require('express');
const app = express();
const port = 3000;

app.get('/', (req, res) => {
  res.send('Hello, World!');
});

app.listen(port, () => {
  console.log(`App running on http://localhost:${port}`);
});
```

### Step 2: Create a Dockerfile

Create a Dockerfile in the root of your project directory:

```sh
# Use the official Node.js image from the Docker Hub
FROM node:14

# Create and change to the app directory
WORKDIR /usr/src/app

# Copy package.json and package-lock.json
COPY package*.json ./

# Install dependencies
RUN npm install

# Copy the rest of the application code
COPY . .

# Expose the application port
EXPOSE 3000

# Start the application
CMD ["node", "index.js"]
```

### Step 3: Build and Run the Docker Image

Build a docker image from the docker file (Dockerization)

```sh
docker build -t node-k8s-app .
```

Run the image in a Docker container at localhost:3000 (Containerization)

```sh
docker run -p 3000:3000 node-k8s-app
```

Till step 3 is enough to test it out the changes locally.

### Step 4: Create Kubernetes Manifests

Create a k8s directory to write the manifest of various kubernetes resources.

#### Deployment Manifest (deployment.yaml)

Create k8s/deployment.yaml:

```sh
apiVersion: apps/v1
kind: Deployment
metadata:
  name: node-k8s-app
spec:
  replicas: 2
  selector:
    matchLabels:
      app: node-k8s-app
  template:
    metadata:
      labels:
        app: node-k8s-app
    spec:
      containers:
      - name: node-k8s-app
        image: node-k8s-app:latest
        ports:
        - containerPort: 3000
```

#### Service Manifest (service.yaml)

Create k8s/service.yaml:

```sh
apiVersion: v1
kind: Service
metadata:
  name: node-k8s-app
spec:
  type: LoadBalancer
  selector:
    app: node-k8s-app
  ports:
    - protocol: TCP
      port: 80
      targetPort: 3000
```

## Step 5.1: Deploy to Docker hub

You can also use AWS ECR or GCP container.

Create tag & push docker image to your's container registery

```sh
docker tag node-k8s-app vishnusureshperumbavoor2/node-k8s-app
docker push vishnusureshperumbavoor2/node-k8s-app
```

Update the image tag in deployment.yaml

```sh
image: vishnusureshperumbavoor2/node-k8s-app:latest
```

Apply the changes to the kubernetes cluster

```sh
kubectl apply -f k8s/deployment.yaml
kubectl apply -f k8s/service.yaml
```

## Step 5.2: Deploy to local Kubernetes cluster

```sh
minikube start
```

build the docker image

```sh
docker build -t node-k8s-app:v2 .
```

Give the same image name in deployment.yaml

```sh
image: node-k8s-app:v2
```

Apply the changes to the kubernetes cluster

```sh
kubectl apply -f k8s/deployment.yaml
kubectl apply -f k8s/service.yaml
```

Run the service on [http://192.168.49.2:30278/](http://192.168.49.2:30278/)

```sh
minikube service node-k8s-app:v2
```

It indicates that the service node-k8s-app is accessible at the IP address 192.168.49.2 and port 30278

### View deployments, pods and services

```sh
kubectl get deployments
kubectl get pods
kubectl get services
```

### Search for images in docker hub

```sh
docker search vishnusureshperumbavoor2
```

### Search for images available on your local machine

```sh
docker images
```

### Kill currently running containers

```sh
docker ps
docker stop <container_id>
```

## Contact

Contact
For any inquiries or feedback, please contact [Vishnu Suresh Perumbavoor](https://vishnusureshperumbavoor.github.io/V-S-P/) at <br> <br>
[![LinkedIn][linkedin-shield]][linkedin-url]
[![github][github-shield]][github-url]
[![Twitter][twitter-shield]][twitter-url]
[![Instagram][instagram-shield]][instagram-url]
[![GMail][gmail-shield]][gmail-url]

[linkedin-shield]: https://img.shields.io/badge/LinkedIn-0077B5?style=for-the-badge&logo=linkedin&logoColor=white
[linkedin-url]: https://www.linkedin.com/in/vishnu-suresh-perumbavoor/
[twitter-shield]: https://img.shields.io/badge/Twitter-1DA1F2?style=for-the-badge&logo=twitter&logoColor=white
[twitter-url]: https://twitter.com/in/vspeeeeee
[instagram-shield]: https://img.shields.io/badge/Instagram-E4405F?style=for-the-badge&logo=instagram&logoColor=white
[instagram-url]: https://www.instagram.com/vishnusureshperumbavoor/
[linktree-shield]: https://img.shields.io/badge/linktree-39E09B?style=for-the-badge&logo=linktree&logoColor=white
[linktree-url]: https://linktr.ee/vishnusureshperumbavoor2.0
[github-shield]: https://img.shields.io/badge/GitHub-100000?style=for-the-badge&logo=github&logoColor=white
[github-url]: https://github.com/vishnusureshperumbavoor
[gmail-shield]: https://img.shields.io/badge/Gmail-D14836?style=for-the-badge&logo=gmail&logoColor=white
[gmail-url]: mailto:vishnusureshperumbavoor@gmail.com
[docker-shield]: https://img.shields.io/badge/Docker-2CA5E0?style=for-the-badge&logo=docker&logoColor=white
[docker-url]: https://hub.docker.com/repository/docker/vishnusureshperumbavoor2/node-k8s-app/general
