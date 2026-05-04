# Lab 00 - Minikube Setup

## Goal

Start a local Kubernetes cluster with Minikube and verify that kubectl can talk to it.

## Estimated Time

40 minutes

## Prerequisites

- Docker is installed and running.
- Minikube is installed.
- kubectl is installed.
- You are in the repository root.

## Step-by-Step Commands

Check Docker:

```bash
docker version
docker ps
```

Start Minikube with the Docker driver:

```bash
minikube start --driver=docker
```

Check Minikube status:

```bash
minikube status
```

Verify the Kubernetes node:

```bash
kubectl get nodes
```

Verify system Pods:

```bash
kubectl get pods -A
```

Check the current kubectl context:

```bash
kubectl config current-context
```

## Expected Output

For `kubectl get nodes`:

```text
NAME       STATUS   ROLES           AGE   VERSION
minikube   Ready    control-plane   ...   v...
```

For `kubectl get pods -A`:

```text
NAMESPACE     NAME                               READY   STATUS    RESTARTS   AGE
kube-system   coredns-...                        1/1     Running   0          ...
kube-system   etcd-minikube                      1/1     Running   0          ...
kube-system   kube-apiserver-minikube            1/1     Running   0          ...
```

For `kubectl config current-context`:

```text
minikube
```

## Validation Commands

```bash
kubectl get nodes
kubectl get pods -A
kubectl config current-context
```

The node should be `Ready`, system Pods should be `Running`, and the context should be `minikube`.

## Common Errors and Fixes

### Docker is not running

Error example:

```text
Exiting due to DRV_NOT_RUNNING
```

Fix:

```bash
docker ps
minikube start --driver=docker
```

If `docker ps` fails, start Docker Desktop or the Docker daemon first.

### kubectl points to the wrong cluster

Check contexts:

```bash
kubectl config get-contexts
```

Fix:

```bash
kubectl config use-context minikube
```

### Minikube is unhealthy

Fix:

```bash
minikube stop
minikube start --driver=docker
```

If that does not work:

```bash
minikube delete
minikube start --driver=docker
```

## Cleanup Commands

For normal class flow, keep Minikube running:

```bash
minikube status
```

To stop the cluster after practice:

```bash
minikube stop
```

To delete the cluster completely:

```bash
minikube delete
```
