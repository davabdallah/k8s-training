# Lab 02 - Rolling Update and Rollback

## Goal

Update a Deployment image, observe the rollout, simulate a bad rollout, and roll back to the previous working version.


## Step-by-Step Commands

Create the Deployment:

```bash
kubectl apply -f day-2/manifests/deployment.yaml
kubectl rollout status deployment/demo-web
```

Check the current image:

```bash
kubectl describe deployment demo-web
```

Update the image:

```bash
kubectl set image deployment/demo-web nginx=nginx:1.26-alpine
```

Watch the rollout:

```bash
kubectl rollout status deployment/demo-web
```

Check rollout history:

```bash
kubectl rollout history deployment/demo-web
```

Check Pods:

```bash
kubectl get pods -l app=demo-web
```

Simulate a bad update:

```bash
kubectl set image deployment/demo-web nginx=nginx:not-a-real-tag
```

Try to watch the rollout:

```bash
kubectl rollout status deployment/demo-web --timeout=30s
```

Inspect the problem:

```bash
kubectl get pods -l app=demo-web
kubectl describe deployment demo-web
kubectl get events --sort-by=.lastTimestamp
```

Roll back to the previous working version:

```bash
kubectl rollout undo deployment/demo-web
```

Wait for the rollback:

```bash
kubectl rollout status deployment/demo-web
```

Check the image again:

```bash
kubectl describe deployment demo-web
```

## Expected Output

For the successful update:

```text
deployment.apps/demo-web image updated
deployment "demo-web" successfully rolled out
```

For the bad update, rollout status should time out:

```text
error: timed out waiting for the condition
```

After rollback:

```text
deployment.apps/demo-web rolled back
deployment "demo-web" successfully rolled out
```

## Validation Commands

```bash
kubectl get deployment demo-web
kubectl get pods -l app=demo-web
kubectl rollout history deployment/demo-web
kubectl describe deployment demo-web
```

The Deployment should be available and should no longer use `nginx:not-a-real-tag`.

## Common Errors and Fixes

### Rollout status times out after the bad image

This is expected in this lab. Roll back:

```bash
kubectl rollout undo deployment/demo-web
kubectl rollout status deployment/demo-web
```

### rollout undo says no revision found

Make sure a Deployment exists and has rollout history:

```bash
kubectl get deployment demo-web
kubectl rollout history deployment/demo-web
```

### Pods are mixed between old and new versions

During rolling updates this can happen temporarily.

Check again after rollout completion:

```bash
kubectl rollout status deployment/demo-web
kubectl get pods -l app=demo-web
```

## Cleanup Commands

```bash
kubectl delete -f day-2/manifests/deployment.yaml
kubectl get deployments
kubectl get pods -l app=demo-web
```
