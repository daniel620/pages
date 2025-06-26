---
title: "Kubernetes Basics"
date: 2025-06-24
tags: ["kubernetes", "k8s", "orchestration", "devops"]
categories: ["devops"]
weight: 2
---

# Kubernetes Basics

Introduction to Kubernetes container orchestration.

## Core Concepts

- **Pods**: Smallest deployable units
- **Services**: Network abstraction over pods
- **Deployments**: Manage pod replicas
- **ConfigMaps**: Configuration data
- **Secrets**: Sensitive data

## Basic Pod Definition

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-app
spec:
  containers:
  - name: app
    image: nginx:latest
    ports:
    - containerPort: 80
```

## Deployment Example

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.20
        ports:
        - containerPort: 80
```

## Common kubectl Commands

```bash
# Get pods
kubectl get pods

# Describe pod
kubectl describe pod my-pod

# Apply configuration
kubectl apply -f deployment.yaml

# Scale deployment
kubectl scale deployment nginx-deployment --replicas=5

# Get logs
kubectl logs my-pod
```
