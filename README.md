
# ☸️ Docker to Kubernetes Playground Notes

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![Made with ❤️](https://img.shields.io/badge/Made%20with-%E2%9D%A4-red.svg)](#)
[![Kubernetes](https://img.shields.io/badge/Kubernetes-1.30-blue?logo=kubernetes)](https://kubernetes.io/)
[![Docker](https://img.shields.io/badge/Docker-blue?logo=docker)](https://www.docker.com/)

> 📘 A practical, step-by-step learning guide for **Docker** and **Kubernetes**, including local setup with **Minikube**, managing containers, working with Pods, Deployments, Services, scaling, rollouts, and debugging — all documented while learning.

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
9. [🧠 Advanced Kubernetes Commands](#-advanced-kubernetes-commands)
10. [🧠 Notes & Tips](#-notes--tips)
11. [📜 License](#-license)

---

## 🚀 Getting Started

This repo documents the **key concepts and practical commands** for working with containers and Kubernetes clusters on macOS (works similarly on Linux).

Before diving in, make sure you have:

* 🐳 **Docker** installed → [Docker Desktop](https://www.docker.com/products/docker-desktop)
* ☸️ **Minikube** & **kubectl** installed (see below)
* 🧠 A basic understanding of terminal commands

---

## 🐳 Docker Basics

### 🔸 Build and run images

```bash
# Build an image from Dockerfile
docker build -t myapp .

# Run a container in background
docker run -d -p 8080:80 myapp

# List running containers
docker ps

# Exec into a running container
docker exec -it <container_id> /bin/bash
```

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
* **Deployment** → Manages Pods, scaling, and rolling updates
* **ReplicaSet** → Ensures the desired number of Pods
* **Service** → Exposes Pods internally or externally
* **Ingress** → Routes HTTP traffic to services
* **ConfigMap / Secret** → Store config & sensitive data
* **Namespace** → Logical grouping of resources

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
3. SSH from your Mac to localhost.

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
# Cluster info & nodes
kubectl cluster-info
kubectl get nodes

# List resources
kubectl get all
kubectl get pods -o wide
kubectl get deployments
kubectl get services

# Describe resources
kubectl describe pod <pod-name>
kubectl describe deployment <deployment-name>

# Apply & delete manifests
kubectl apply -f deployment.yaml
kubectl delete -f deployment.yaml

# Minikube dashboard
minikube dashboard
```

---

## 🧠 Advanced Kubernetes Commands

### 📌 **Namespaces**

```bash
# List namespaces
kubectl get ns

# Create a namespace
kubectl create ns dev

# Apply resources in a specific namespace
kubectl apply -f app.yaml -n dev

# Switch context temporarily
kubectl get pods -n kube-system
```

---

### 🧰 **Logs & Troubleshooting**

```bash
# View pod logs
kubectl logs <pod-name>

# View logs of specific container in a pod
kubectl logs <pod-name> -c <container-name>

# Follow logs (like tail -f)
kubectl logs -f <pod-name>

# Describe a failing pod
kubectl describe pod <pod-name>

# Check cluster events
kubectl get events --sort-by=.metadata.creationTimestamp
```

---

### 📈 **Scaling & Rollouts**

```bash
# Scale a deployment
kubectl scale deployment myapp --replicas=3

# Check rollout status
kubectl rollout status deployment/myapp

# Rollout history
kubectl rollout history deployment/myapp

# Rollback
kubectl rollout undo deployment/myapp
```

---

### 🔄 **Resource Management**

```bash
# Get CPU & memory usage (requires metrics server)
kubectl top nodes
kubectl top pods

# Delete resources
kubectl delete pod <pod-name>
kubectl delete deployment <deployment-name>
kubectl delete svc <service-name>

# Delete all resources in namespace
kubectl delete all --all -n dev
```

---

### 🧪 **YAML Debugging & Dry Runs**

```bash
# Generate manifest without applying
kubectl create deployment myapp --image=nginx --dry-run=client -o yaml > deployment.yaml

# Validate YAML before applying
kubectl apply -f deployment.yaml --dry-run=server
```

---

### 🧠 **Aliases (Optional)**

You can add these to `~/.zshrc` or `~/.bashrc` for convenience:

```bash
alias k='kubectl'
alias kgp='kubectl get pods'
alias kga='kubectl get all'
alias kdp='kubectl describe pod'
alias kaf='kubectl apply -f'
alias kdf='kubectl delete -f'
```

Then you can do:

```bash
k get pods
k apply -f deployment.yaml
```

---

## 🧠 Notes & Tips

* ✅ **`kubectl exec` > SSH** — Pods are ephemeral; SSH is not the norm.
* 🧰 Use `kubectl logs <pod>` and `kubectl describe` for debugging containers.
* 🌱 Minikube + Docker gives a near-production environment locally.
* 💡 If a pod doesn't have Bash, use `sh`.
* 🌀 Use namespaces to keep environments isolated (e.g., dev, staging).
* 🔒 Avoid running SSH servers in production pods unless absolutely needed.
* 🚀 Use `kubectl apply --dry-run` for safe testing before deploying.

---

## 🛣️ Learning Roadmap

- [x] ✅ Docker basics (build, run, cleanup)
- [x] ✅ Minikube setup on Mac (Docker driver)
- [x] ✅ Basic kubectl commands (pods, deployments, services)
- [x] ✅ Port-forwarding & dashboards
- [ ] ⏳ Ingress + TLS setup
- [ ] ⏳ Helm basics
- [ ] ⏳ StatefulSets & Persistent Volumes
- [ ] ⏳ CI/CD Integration (GitHub Actions)


## 📜 License

This project is licensed under the [MIT License](LICENSE).

---

### ✍️ Author

Maintained by [Sushant Gautam](https://github.com/sushant097)

Learning DevOps & Cloud — documenting every step 🚀