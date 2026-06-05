# Lab 01 - Deployment Create and Scale

## Goal

Create a Deployment, inspect the ReplicaSet and Pods it manages, and scale the application.


## Prerequisites

- Minikube is running.
- kubectl context is set to `minikube`.
- You are in the repository root.

## Step-by-Step Commands

Open the Deployment manifest:

```bash
git clone https://github.com/davabdallah/k8s-training.git
cd k8s-training
cat day-2/manifests/deployment.yaml
```

Create the Deployment:

```bash
kubectl apply -f day-2/manifests/deployment.yaml
```

Check the Deployment:

```bash
kubectl get deployments
```

Check the ReplicaSet:

```bash
kubectl get replicasets
```

Check the Pods:

```bash
kubectl get pods -l app=demo-web
```

Wait for rollout completion:

```bash
kubectl rollout status deployment/demo-web
```

Scale to 4 replicas:

```bash
kubectl scale deployment demo-web --replicas=4
```

Check the new Pod count:

```bash
kubectl get pods -l app=demo-web
```

Scale back to 2 replicas:

```bash
kubectl scale deployment demo-web --replicas=2
```

## Expected Output

For `kubectl get deployments`:

```text
NAME       READY   UP-TO-DATE   AVAILABLE   AGE
demo-web   2/2     2            2           ...
```

After scaling to 4 replicas:

```text
deployment.apps/demo-web scaled
```

For `kubectl get pods -l app=demo-web`:

```text
NAME                        READY   STATUS    RESTARTS   AGE
demo-web-...                1/1     Running   0          ...
demo-web-...                1/1     Running   0          ...
demo-web-...                1/1     Running   0          ...
demo-web-...                1/1     Running   0          ...
```

## Validation Commands

```bash
kubectl get deployment demo-web
kubectl get replicasets
kubectl get pods -l app=demo-web
kubectl describe deployment demo-web
```

The Deployment should show the desired number of replicas as ready.

## Common Errors and Fixes

### Deployment has zero ready Pods

Inspect the Deployment and Pods:

```bash
kubectl describe deployment demo-web
kubectl get pods -l app=demo-web
kubectl describe pod <pod-name>
```

Common causes include image pull errors or not enough cluster resources.

### Scale command changes nothing

Check the Deployment name:

```bash
kubectl get deployments
```

Then rerun:

```bash
kubectl scale deployment demo-web --replicas=4
```

### Pods are still terminating

This can happen briefly after scaling down.

Check again:

```bash
kubectl get pods -l app=demo-web
```

## Cleanup Commands

```bash
kubectl delete -f day-2/manifests/deployment.yaml
kubectl get deployments
kubectl get pods -l app=demo-web
```
