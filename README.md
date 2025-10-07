# 🐳 Kubernetes & Docker Guide

A practical, step-by-step guide to learning **Docker** and **Kubernetes**, including local cluster setup with **Minikube**, container management, pod access, deployments, and debugging techniques — all documented while learning.

> 📌 Ideal for beginners & intermediate DevOps learners who want a clean reference to revisit.

---

## 📂 Table of Contents

1. [🚀 Getting Started](#-getting-started)
2. [🐳 Docker Basics](#-docker-basics)
3. [☸️ Kubernetes Basics](#️-kubernetes-basics)
4. [🧰 Installing Minikube & kubectl](#-installing-minikube--kubectl)
5. [🔐 Accessing Minikube Node](#-accessing-minikube-node)
6. [💻 Connecting to Pods (kubectl exec / SSH)](#-connecting-to-pods-kubectl-exec--ssh)
7. [🌐 Port Forwarding & Exposing Services](#-port-forwarding--exposing-services)
8. [⚡ Useful Commands](#-useful-commands)
9. [🧠 Notes & Tips](#-notes--tips)
10. [📜 License](#-license)

---

## 🚀 Getting Started

This repo documents the **key concepts and practical commands** for working with containers and Kubernetes clusters on macOS (works similarly on Linux).  

Before diving in, make sure you have:

- 🐳 **Docker** installed → [Docker Desktop](https://www.docker.com/products/docker-desktop)
- ☸️ **Minikube** & **kubectl** installed (see below)
- 🧠 A basic understanding of terminal commands

---

## 🐳 Docker Basics

### 🔸 Build and run images

```bash
# Build an image
docker build -t myapp .

# Run a container
docker run -d -p 8080:80 myapp

# List containers
docker ps

# Exec into a running container
docker exec -it <container_id> /bin/bash
````

### 🔸 Clean up

```bash
docker stop <id>
docker rm <id>
docker rmi <image>
docker system prune -a
```

---

## ☸️ Kubernetes Basics

Kubernetes (K8s) lets you **orchestrate containers** — deploy, scale, and manage them across clusters.

Key objects:

* **Pod** → Smallest deployable unit (runs containers)
* **Deployment** → Manages Pods, scaling, and updates
* **Service** → Exposes Pods internally or externally
* **Ingress** → Routes HTTP traffic to services
* **ConfigMap / Secret** → Inject config & secrets

---

## 🧰 Installing Minikube & kubectl

### 🛠 Install `kubectl`

```bash
brew install kubectl
kubectl version --client
```

### 🛠 Install Minikube

```bash
brew install minikube
minikube start --driver=docker
```

Check cluster status:

```bash
kubectl get nodes
```

You should see your local Minikube node ready ✅

---

## 🔐 Accessing Minikube Node

### ⏩ Direct SSH

```bash
minikube ssh
```

This gives you a shell inside the Minikube node container.

Or using Docker:

```bash
docker exec -it minikube /bin/bash
```

---

## 💻 Connecting to Pods (kubectl exec / SSH)

### ⚡ `kubectl exec` (Recommended)

```bash
kubectl get pods
kubectl exec -it <pod-name> -- /bin/sh
```

If multiple containers:

```bash
kubectl exec -it <pod-name> -c <container-name> -- /bin/bash
```

### 🧪 Optional: Real SSH to a Pod

Kubernetes doesn’t run SSH by default. But you can:

1. Install OpenSSH inside a pod.
2. Forward port 22 locally.
3. SSH from Mac to localhost.

```bash
kubectl run ssh-pod --image=ubuntu --restart=Never --command -- sleep infinity
kubectl exec -it ssh-pod -- bash
apt update && apt install -y openssh-server
service ssh start
passwd
kubectl port-forward ssh-pod 2222:22
ssh root@127.0.0.1 -p 2222
```

---

## 🌐 Port Forwarding & Exposing Services

### Port-forward directly to a pod

```bash
kubectl port-forward <pod-name> 8080:80
```

### Expose a deployment as a service

```bash
kubectl expose deployment myapp --type=NodePort --port=80
minikube service myapp
```

This opens the service in your browser automatically.

---

## ⚡ Useful Commands

```bash
# Check cluster info
kubectl cluster-info

# Get all resources
kubectl get all

# Describe a resource
kubectl describe pod <pod-name>

# Apply or delete YAML
kubectl apply -f deployment.yaml
kubectl delete -f deployment.yaml

# Access Minikube dashboard
minikube dashboard
```

---

## 🧠 Notes & Tips

* ✅ **`kubectl exec` > SSH** — Pods are ephemeral; SSH is not the norm.
* 🧰 Use `kubectl logs <pod>` for debugging containers.
* 🌱 Minikube + Docker gives a near-production environment locally.
* 💡 If a pod doesn't have Bash, use `sh`.
* 🔒 Avoid running SSH servers in production pods unless absolutely needed.

---

## 📜 License

This project is licensed under the [MIT License](LICENSE).

---

### ✍️ Author

Maintained by [Sushant Gautam](https://github.com/sushant097)
Learning DevOps & Cloud — documenting every step 🚀

```
