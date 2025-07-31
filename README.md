# Todo App – DevOps Project

> A simple Node.js + MongoDB application to demonstrate complete DevOps automation using **GitHub Actions**, **Docker**, **Ansible**, and **Argo CD**..

---

## Overview

This project is a training exercise to implement **CI/CD** pipelines and apply modern **DevOps principles**. The app is deployed on a Kubernetes cluster with full automation from provisioning the server to production deployment.

---

##  Tech Stack

| Tool             | Purpose                                                |
|------------------|--------------------------------------------------------|
| **Docker**       | Containerizing the application                         |
| **GitHub Actions** | CI Pipeline: build and push Docker image              |
| **Ansible**      | Provision remote Ubuntu VM (install Docker, Kubernetes via kubeadm) |
| **Argo CD**      | Continuous Deployment to Kubernetes                    |
| **Kubernetes**   | Container orchestration (via kubeadm)                  |


##  CI/CD Workflow

###  Continuous Integration – GitHub Actions

- On every push to `main`, GitHub Actions:
  - Builds Docker image
  - Pushes it to Docker Hub

```yaml
# Example
on: [push]
jobs:
  docker:
    steps:
      - name: Build and push image
```
###  Continuous Deployment – Argo CD
  - Argo CD watches the Git repository

  - Automatically applies updated Kubernetes manifests

  - Supports automatic image updates with Argo CD Image Updater
### Infrastructure Automation – Ansible
  Using Ansible to provision a fresh Ubuntu VM:

Set remote IP in ansible/inventory/hosts.ini

Run playbook:

```bash
docker run --rm -it \
  -v $(pwd)/ansible:/ansible \
  ansible/ansible \
  ansible-playbook -i inventory/hosts.ini playbook.yml
```
Playbook installs:

- Docker

- kubeadm

- Initializes Kubernetes cluster

 - Configures kubectl
### 📦 Containerization – Docker
Simple Dockerfile for Node.js:

```Dockerfile
Copy
Edit
FROM node:18
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
EXPOSE 3000
CMD ["npm", "start"]
```
### Kubernetes – via kubeadm
We use kubeadm (not Minikube or MicroK8s) to set up a production-like cluster. Key K8s components:
- Namespace: Isolates app resources
- Deployment & Service: For both Node.js and MongoDB
- PVC: For MongoDB persistent storage
- HPA: Auto-scaling based on CPU usage

### Image Tag Updates
Use Argo CD Image Updater with annotations like:


Concept	How It's Done in This Project
IaC (Infrastructure as Code)	Provision with Ansible
CI	GitHub Actions builds & pushes Docker image
CD	Argo CD pulls latest code and deploys to K8s
Containerization	Dockerfile used to encapsulate app and dependencies
Orchestration	Kubernetes manages pods, scaling, and networking
