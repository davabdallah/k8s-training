# Lab 01 - kubectl Inspection

## Goal

Use kubectl to inspect cluster information, nodes, namespaces, Pods, and Kubernetes API resources.

## Prerequisites

- Minikube is running.
- kubectl context is set to `minikube`.

## Step-by-Step Commands

Check cluster information:

```bash
kubectl cluster-info
```

Show the current context:

```bash
kubectl config current-context
```

List nodes:

```bash
kubectl get nodes
kubectl get nodes -o wide
```

List namespaces:

```bash
kubectl get namespaces
```

List Pods in the default namespace:

```bash
kubectl get pods
```

List Pods in all namespaces:

```bash
kubectl get pods -A
```

List common API resources:

```bash
kubectl api-resources
```

Ask kubectl for Pod documentation:

```bash
kubectl explain pod
kubectl explain pod.spec
```

Inspect the Minikube node:

```bash
kubectl describe node minikube
```

## Expected Output

For `kubectl get namespaces`:

```text
NAME              STATUS   AGE
default           Active   ...
kube-node-lease   Active   ...
kube-public       Active   ...
kube-system       Active   ...
```

For `kubectl get pods` in the default namespace:

```text
No resources found in default namespace.
```

For `kubectl get pods -A`, you should see Kubernetes system Pods in the `kube-system` namespace.

## Validation Commands

```bash
kubectl get nodes
kubectl get namespaces
kubectl get pods -A
```

The node should be visible, namespaces should be listed, and system Pods should be running.

## Common Errors and Fixes

### The connection to the server was refused

Check Minikube:

```bash
minikube status
```

Start it if needed:

```bash
minikube start --driver=docker
```

### You are using the wrong context

Check:

```bash
kubectl config current-context
```

Fix:

```bash
kubectl config use-context minikube
```

### The output is too large

Use a specific resource or namespace:

```bash
kubectl get pods -n kube-system
kubectl get service -n default
```

## Cleanup Commands

This lab only inspects the cluster. There are no resources to delete.

You can verify that the default namespace is still empty:

```bash
kubectl get pods
```
