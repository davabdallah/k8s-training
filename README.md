# Kubernetes 3-Day Instructor-Led Training

Hands-on Kubernetes training 

## Course Overview

Over 3 days, students will learn the daily workflow of working with Kubernetes:

- Day 1: Minikube, kubectl inspection, Pods, YAML, logs, events, labels, and namespaces.
- Day 2: Deployments, scaling, rolling updates, rollback, Services, ClusterIP, kube-proxy flow, ConfigMaps, and Secrets.
- Day 3: Ingress, liveness and readiness probes, PVC storage, and a small frontend/backend mini project.

Each lab includes goals, estimated time, commands, expected output, validation, common errors, and cleanup steps.

## Prerequisites

Before starting, students should be comfortable running commands in a terminal and editing YAML files.

Required tools:

- Docker
- Minikube
- kubectl
- Git
- VS Code or any editor

For students using Ubuntu inside WSL 2, follow [WSL Ubuntu tool installation](setup/wsl-ubuntu-install.md) before starting Day 1.

## Day-by-Day Agenda

### Day 1 - Kubernetes Basics and Pods

- Course setup and Minikube startup
- Inspecting cluster state with kubectl
- Creating Pods imperatively
- Creating Pods from YAML
- Reading logs and events
- Debugging common Pod issues
- Labels, selectors, and namespaces

### Day 2 - Deployments and Services

- Creating Deployments
- Scaling replicas
- Rolling updates
- Rollback
- Services and ClusterIP
- Service selectors and endpoints
- How kube-proxy connects traffic to Pods
- ConfigMaps
- Secrets

### Day 3 - Ingress, Probes, Storage, and Mini Project

- Ingress controller setup in Minikube
- Routing HTTP traffic with Ingress
- Liveness and readiness probes
- PersistentVolumeClaims
- Final frontend/backend mini project

## Quick Start

Clone the repository and enter the training directory:

```bash
git clone https://github.com/davabdallah/k8s-training.git
cd k8s-training
```

Start Minikube with the Docker driver:

```bash
minikube start --driver=docker
```

Verify the cluster:

```bash
kubectl get nodes
kubectl get pods -A
kubectl config current-context
```

Expected context:

```text
minikube
```

## Labs

### Setup

- [WSL Ubuntu tool installation](setup/wsl-ubuntu-install.md)
- [Minikube setup](setup/minikube-setup.md)
- [General troubleshooting](setup/troubleshooting.md)

### Day 1

- [Day 1 overview](day-1/README.md)
- [Lab 00 - Minikube setup](day-1/lab-00-minikube-setup.md)
- [Lab 01 - kubectl inspection](day-1/lab-01-kubectl-inspection.md)
- [Lab 02 - Create a Pod imperatively](day-1/lab-02-create-pod-imperative.md)
- [Lab 03 - Create a Pod with YAML](day-1/lab-03-create-pod-yaml.md)
- [Lab 04 - Logs, events, and debugging](day-1/lab-04-logs-events-debugging.md)
- [Lab 05 - Labels and namespaces](day-1/lab-05-labels-namespaces.md)

### Day 2

- [Day 2 overview](day-2/README.md)
- [Lab 01 - Deployment create and scale](day-2/lab-01-deployment-create-scale.md)
- [Lab 02 - Rolling update and rollback](day-2/lab-02-rolling-update-rollback.md)
- [Lab 03 - Service ClusterIP](day-2/lab-03-service-clusterip.md)
- [Lab 04 - Service and kube-proxy flow](day-2/lab-04-service-kube-proxy-flow.md)
- [Lab 05 - ConfigMap and Secret](day-2/lab-05-configmap-secret.md)

### Day 3

- [Day 3 overview](day-3/README.md)
- [Lab 01 - Ingress](day-3/lab-01-ingress.md)
- [Lab 02 - Liveness and readiness probes](day-3/lab-02-liveness-readiness-probes.md)
- [Lab 03 - PVC storage](day-3/lab-03-storage-pvc.md)
- [Mini project](day-3/mini-project.md)

### Cheatsheets

- [kubectl cheatsheet](cheatsheets/kubectl-cheatsheet.md)
- [Troubleshooting cheatsheet](cheatsheets/troubleshooting-cheatsheet.md)

## Repository Notes

- All commands are written for a local Minikube cluster.
- YAML examples use stable Kubernetes API versions.
- Secrets in this repository are for training only. Do not use these values in real systems.
- Cleanup commands are included so students can safely repeat labs.
