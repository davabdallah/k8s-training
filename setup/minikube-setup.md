# Minikube Setup

Use this guide before Day 1 if you want to verify that every student's machine is ready.

## Required Tools

Install these tools before class:

- Docker
- Minikube
- kubectl
- Git
- VS Code or any editor

If students are using Ubuntu inside WSL 2, install kubectl and Minikube with [WSL Ubuntu tool installation](wsl-ubuntu-install.md) first.

## Verify Tool Installation

```bash
docker version
minikube version
kubectl version --client
git --version
```

Expected output examples:

```text
Client:
 Version: Docker Engine ...
```

```text
minikube version: ...
```

```text
Client Version: ...
```

## Start Minikube

Start the cluster with the Docker driver:

```bash
minikube start --driver=docker
```

Expected output includes:

```text
Done! kubectl is now configured to use "minikube" cluster and "default" namespace by default
```

## Verify Cluster Access

```bash
kubectl get nodes
kubectl get pods -A
kubectl config current-context
```

Expected output:

```text
NAME       STATUS   ROLES           AGE   VERSION
minikube   Ready    control-plane   ...   v...
```

```text
minikube
```

## Useful Minikube Commands

```bash
minikube status
minikube ip
minikube dashboard
minikube stop
minikube delete
```

## Reset the Local Cluster

Use this only when you want a fresh cluster:

```bash
minikube delete
minikube start --driver=docker
```

## Notes for Instructors

- Ask students to keep Docker running during the full course.
- If a lab behaves strangely, first check `minikube status`.
- If the cluster is broken beyond quick repair, delete and recreate it.
