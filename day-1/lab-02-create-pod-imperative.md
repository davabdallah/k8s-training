# Lab 02 - Create a Pod Imperatively

## Goal

Create a Pod using a kubectl command, inspect it, view logs, and delete it.

## Estimated Time

45 minutes

## Prerequisites

- Minikube is running.
- kubectl context is set to `minikube`.
- You understand what a Docker image is.

## Step-by-Step Commands

Create a Pod from the `nginx` image:

```bash
kubectl run nginx-imperative --image=nginx:1.25-alpine --restart=Never --port=80
```

List Pods:

```bash
kubectl get pods
```

Wait until the Pod is ready:

```bash
kubectl wait --for=condition=Ready pod/nginx-imperative --timeout=120s
```

Inspect the Pod:

```bash
kubectl describe pod nginx-imperative
```

View logs:

```bash
kubectl logs nginx-imperative
```

Run a command inside the container:

```bash
kubectl exec nginx-imperative -- nginx -v
```

Forward a local port to the Pod:

```bash
kubectl port-forward pod/nginx-imperative 8080:80
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

For `kubectl exec nginx-imperative -- nginx -v`:

```text
nginx version: nginx/...
```

For `curl http://localhost:8080`, the response should include:

```text
Welcome to nginx!
```

## Validation Commands

```bash
kubectl get pod nginx-imperative
kubectl describe pod nginx-imperative
kubectl logs nginx-imperative
```

The Pod should be `Running` and `READY` should be `1/1`.

## Common Errors and Fixes

### Pod stays in ContainerCreating

Wait a little longer, then inspect events:

```bash
kubectl describe pod nginx-imperative
```

### Port 8080 is already in use

Use another local port:

```bash
kubectl port-forward pod/nginx-imperative 8081:80
curl http://localhost:8081
```

### curl does not work

Make sure port forwarding is still running in another terminal.

## Cleanup Commands

Delete the Pod:

```bash
kubectl delete pod nginx-imperative
```

Verify deletion:

```bash
kubectl get pods
```
