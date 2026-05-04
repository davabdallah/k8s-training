# Lab 02 - Liveness and Readiness Probes

## Goal

Add liveness and readiness probes to a Deployment and observe how readiness affects traffic availability.

## Estimated Time

70 minutes

## Prerequisites

- Minikube is running.
- kubectl context is set to `minikube`.
- You are in the repository root.

## Step-by-Step Commands

Open the manifest:

```bash
cat day-3/manifests/probes-deployment.yaml
```

Create the Deployment:

```bash
kubectl apply -f day-3/manifests/probes-deployment.yaml
```

Wait for rollout:

```bash
kubectl rollout status deployment/probes-demo
```

Check Pods:

```bash
kubectl get pods -l app=probes-demo
```

Describe one Pod and find the probe configuration:

```bash
POD_NAME=$(kubectl get pods -l app=probes-demo -o jsonpath='{.items[0].metadata.name}')
kubectl describe pod "$POD_NAME"
```

Create a temporary Service for testing:

```bash
kubectl expose deployment probes-demo --name=probes-demo --port=80 --target-port=80
```

Create a client Pod:

```bash
kubectl run probe-test --image=busybox:1.36 --restart=Never -- sleep 3600
kubectl wait --for=condition=Ready pod/probe-test --timeout=120s
```

Call the Service:

```bash
kubectl exec probe-test -- wget -qO- http://probes-demo
```

Break the readiness probe path:

```bash
kubectl patch deployment probes-demo --type='json' -p='[{"op":"replace","path":"/spec/template/spec/containers/0/readinessProbe/httpGet/path","value":"/not-found"}]'
```

Watch Pods become not ready:

```bash
kubectl get pods -l app=probes-demo
kubectl get endpoints probes-demo
```

Fix the Deployment by applying the original manifest:

```bash
kubectl apply -f day-3/manifests/probes-deployment.yaml
kubectl rollout status deployment/probes-demo
```

## Expected Output

Before breaking readiness:

```text
NAME                           READY   STATUS    RESTARTS   AGE
probes-demo-...                1/1     Running   0          ...
```

After breaking readiness, Pods may show:

```text
NAME                           READY   STATUS    RESTARTS   AGE
probes-demo-...                0/1     Running   0          ...
```

When endpoints are removed:

```text
NAME          ENDPOINTS   AGE
probes-demo   <none>      ...
```

After fixing:

```text
deployment "probes-demo" successfully rolled out
```

## Validation Commands

```bash
kubectl get deployment probes-demo
kubectl get pods -l app=probes-demo
kubectl get endpoints probes-demo
kubectl exec probe-test -- wget -qO- http://probes-demo
```

The application is healthy when Pods are `1/1` ready and the Service has endpoints.

## Common Errors and Fixes

### Patch command fails

Copy the command exactly. JSON patch syntax is strict.

You can still observe the normal probes with:

```bash
kubectl describe pod "$POD_NAME"
```

### Service already exists

Delete and recreate it:

```bash
kubectl delete service probes-demo
kubectl expose deployment probes-demo --name=probes-demo --port=80 --target-port=80
```

### Pods stay not ready after fixing

Check the probe path:

```bash
kubectl describe pod "$POD_NAME"
```

Reapply the manifest and wait:

```bash
kubectl apply -f day-3/manifests/probes-deployment.yaml
kubectl rollout status deployment/probes-demo
```

## Cleanup Commands

```bash
kubectl delete pod probe-test --ignore-not-found
kubectl delete service probes-demo --ignore-not-found
kubectl delete -f day-3/manifests/probes-deployment.yaml
kubectl get pods -l app=probes-demo
```
