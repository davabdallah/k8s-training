# Lab 04 - Logs, Events, and Debugging

## Goal

Practice debugging Pods using logs, describe output, events, and common status messages.

## Estimated Time

60 minutes

## Prerequisites

- Minikube is running.
- kubectl context is set to `minikube`.
- You can create and delete Pods.

## Step-by-Step Commands

Create a Pod that prints a few log lines:

```bash
kubectl run log-demo --image=busybox:1.36 --restart=Never -- sh -c 'for i in 1 2 3; do echo "hello from log-demo $i"; sleep 2; done'
```

Wait for it to finish:

```bash
kubectl get pods
```

View logs:

```bash
kubectl logs log-demo
```

Describe the Pod:

```bash
kubectl describe pod log-demo
```

Create a Pod with an invalid image tag:

```bash
kubectl run broken-image --image=nginx:not-a-real-tag --restart=Never
```

Watch the Pod status:

```bash
kubectl get pod broken-image
```

Inspect details and events:

```bash
kubectl describe pod broken-image
kubectl get events --sort-by=.lastTimestamp
```

Fix the issue by deleting the broken Pod and creating it with a valid image:

```bash
kubectl delete pod broken-image
kubectl run fixed-image --image=nginx:1.25-alpine --restart=Never --port=80
```

Wait for the fixed Pod:

```bash
kubectl wait --for=condition=Ready pod/fixed-image --timeout=120s
```

## Expected Output

For `kubectl logs log-demo`:

```text
hello from log-demo 1
hello from log-demo 2
hello from log-demo 3
```

For the broken image Pod:

```text
NAME           READY   STATUS             RESTARTS   AGE
broken-image   0/1     ImagePullBackOff   0          ...
```

The exact status may briefly show `ErrImagePull` before `ImagePullBackOff`.

For the fixed Pod:

```text
pod/fixed-image condition met
```

## Validation Commands

```bash
kubectl logs log-demo
kubectl get pod broken-image --ignore-not-found
kubectl get pod fixed-image
kubectl describe pod fixed-image
```

The fixed Pod should be `Running` and ready.

## Common Errors and Fixes

### Logs are empty

The container may not have printed anything yet, or the command may have failed.

Check:

```bash
kubectl describe pod log-demo
kubectl get pod log-demo
```

### Pod shows ImagePullBackOff

This usually means the image name or tag is wrong.

Check:

```bash
kubectl describe pod broken-image
```

Fix by using a valid image:

```bash
kubectl delete pod broken-image
kubectl run fixed-image --image=nginx:1.25-alpine --restart=Never --port=80
```

### Pod is Completed, not Running

This is expected for `log-demo` because its command finishes.

## Cleanup Commands

```bash
kubectl delete pod log-demo fixed-image broken-image --ignore-not-found
kubectl get pods
```
