# Mini Project - Frontend and Backend Application

## Goal

Deploy a small frontend/backend application using Deployments, Services, ConfigMaps, and Ingress.


## Prerequisites

- Minikube is running.
- kubectl context is set to `minikube`.
- The Minikube Ingress addon is enabled.
- You are in the repository root.

## Architecture

The project contains:

- `frontend` Deployment and Service
- `backend` Deployment and Service
- ConfigMaps for frontend and backend content
- Ingress host `training.local`
- `/` route to the frontend
- `/api` route to the backend

## Step-by-Step Commands

Enable Ingress if it is not already enabled:

```bash
minikube addons enable ingress
kubectl get pods -n ingress-nginx
```

Open the manifests:

```bash
cat day-3/manifests/frontend.yaml
cat day-3/manifests/backend.yaml
cat day-3/manifests/project-ingress.yaml
```

Apply the frontend:

```bash
kubectl apply -f day-3/manifests/frontend.yaml
```

Apply the backend:

```bash
kubectl apply -f day-3/manifests/backend.yaml
```

Apply the Ingress:

```bash
kubectl apply -f day-3/manifests/project-ingress.yaml
```

Wait for both Deployments:

```bash
kubectl rollout status deployment/frontend
kubectl rollout status deployment/backend
```

Inspect all project resources:

```bash
kubectl get deployments
kubectl get pods -l app=frontend
kubectl get pods -l app=backend
kubectl get services
kubectl get configmaps
kubectl get ingress project-ingress
```

Create a client Pod for internal testing:

```bash
kubectl run project-test --image=busybox:1.36 --restart=Never -- sleep 3600
kubectl wait --for=condition=Ready pod/project-test --timeout=120s
```

Test the frontend Service:

```bash
kubectl exec project-test -- wget -qO- http://frontend
```

Test the backend Service:

```bash
kubectl exec project-test -- wget -qO- http://backend/api
```

Get the Minikube IP:

```bash
MINIKUBE_IP=$(minikube ip)
echo "$MINIKUBE_IP"
```

Test the frontend through Ingress:

```bash
curl --resolve training.local:80:${MINIKUBE_IP} http://training.local/
```

Test the backend through Ingress:

```bash
curl --resolve training.local:80:${MINIKUBE_IP} http://training.local/api
```

## Expected Output

For Deployments:

```text
NAME       READY   UP-TO-DATE   AVAILABLE   AGE
frontend   2/2     2            2           ...
backend    2/2     2            2           ...
```

For Services:

```text
NAME         TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)   AGE
frontend     ClusterIP   ...             <none>        80/TCP    ...
backend      ClusterIP   ...             <none>        80/TCP    ...
```

For frontend output:

```text
Kubernetes Training Frontend
```

For backend output:

```text
{"service":"backend","message":"Hello from the backend service","source":"ConfigMap"}
```

## Validation Commands

```bash
kubectl get pods -l app=frontend
kubectl get pods -l app=backend
kubectl get endpoints frontend
kubectl get endpoints backend
kubectl get ingress project-ingress
kubectl exec project-test -- wget -qO- http://frontend
kubectl exec project-test -- wget -qO- http://backend/api
curl --resolve training.local:80:${MINIKUBE_IP} http://training.local/
curl --resolve training.local:80:${MINIKUBE_IP} http://training.local/api
```

The project is complete when both internal Service tests and both Ingress tests work.

## Common Errors and Fixes

### Ingress returns 404

Check host and paths:

```bash
kubectl describe ingress project-ingress
```

Use the exact host:

```bash
curl --resolve training.local:80:${MINIKUBE_IP} http://training.local/
```

### Service has no endpoints

Check labels and selectors:

```bash
kubectl get pods --show-labels
kubectl describe service frontend
kubectl describe service backend
kubectl get endpoints frontend
kubectl get endpoints backend
```

### ConfigMap content does not change immediately

Restart the Deployment:

```bash
kubectl rollout restart deployment/frontend
kubectl rollout restart deployment/backend
kubectl rollout status deployment/frontend
kubectl rollout status deployment/backend
```

### Backend `/api` route does not respond

Check that the backend ConfigMap contains a key named `api`:

```bash
kubectl describe configmap backend-content
kubectl exec project-test -- wget -qO- http://backend/api
```

## Cleanup Commands

```bash
kubectl delete pod project-test --ignore-not-found
kubectl delete -f day-3/manifests/project-ingress.yaml
kubectl delete -f day-3/manifests/frontend.yaml
kubectl delete -f day-3/manifests/backend.yaml
kubectl get deployments
kubectl get services
kubectl get ingress
```
