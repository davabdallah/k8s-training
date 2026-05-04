# Lab 02 - Create a Pod Imperatively

## Goal

Create a Pod using a kubectl command, inspect it, view logs, and delete it.


## Prerequisites

- Minikube is running.
- kubectl context is set to `minikube`.
- You understand what a Docker image is.

## Step-by-Step Commands

Create a Pod from the `nginx` image:

```bash
kubectl run web --image=nginx:1.27-alpine --port=80
```

List Pods:

```bash
kubectl get pods
```

Describe the Pod:

```bash
kubectl describe pod web
```


Run a command inside the container:

```bash
kubectl exec web -- nginx -v
```

Forward a local port to the Pod:

```bash
kubectl port-forward pod/web 8080:80
```

Open a second terminal and test the Pod:

```bash
curl http://localhost:8080
```

Stop port forwarding with `Ctrl+C` in the first terminal.

## Expected Output

For `kubectl get pods`:

```text
NAME               READY   STATUS    RESTARTS   AGE
nginx-imperative   1/1     Running   0          ...
```

For `kubectl exec web -- nginx -v`:

```text
nginx version: nginx/...
```

For `curl http://localhost:8080`, the response should include:

```text
Welcome to nginx!
```

## Validation Commands

```bash
kubectl get pod web
kubectl describe pod web
kubectl logs web
```

The Pod should be `Running` and `READY` should be `1/1`.

## Common Errors and Fixes

### Pod stays in ContainerCreating

Wait a little longer, then inspect events:

```bash
kubectl describe pod web
```

### Port 8080 is already in use

Use another local port:

```bash
kubectl port-forward pod/web 8081:80
curl http://localhost:8081
```

### curl does not work

Make sure port forwarding is still running in another terminal.

## Cleanup Commands

Delete the Pod:

```bash
kubectl delete pod web
```

Verify deletion:

```bash
kubectl get pods
```
