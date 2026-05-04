# Lab 03 - Create a Pod with YAML

## Goal

Create a Pod from a YAML manifest and learn how declarative Kubernetes configuration works.

## Estimated Time

50 minutes

## Prerequisites

- Minikube is running.
- kubectl context is set to `minikube`.
- You are in the repository root.

## Step-by-Step Commands

Open the manifest:

```bash
cat day-1/manifests/pod.yaml
```

Apply the manifest:

```bash
kubectl apply -f day-1/manifests/pod.yaml
```

List Pods:

```bash
kubectl get pods
```

Wait for the Pod:

```bash
kubectl wait --for=condition=Ready pod/nginx-yaml --timeout=120s
```

Inspect the Pod:

```bash
kubectl describe pod nginx-yaml
```

Print the live Pod as YAML:

```bash
kubectl get pod nginx-yaml -o yaml
```

Forward a local port:

```bash
kubectl port-forward pod/nginx-yaml 8080:80
```

Open a second terminal and test:

```bash
curl http://localhost:8080
```

Stop port forwarding with `Ctrl+C`.

## Expected Output

For `kubectl apply`:

```text
pod/nginx-yaml created
```

For `kubectl get pods`:

```text
NAME         READY   STATUS    RESTARTS   AGE
nginx-yaml   1/1     Running   0          ...
```

For `curl http://localhost:8080`, the response should include:

```text
Welcome to nginx!
```

## Validation Commands

```bash
kubectl get pod nginx-yaml --show-labels
kubectl describe pod nginx-yaml
kubectl get pod nginx-yaml -o wide
```

The Pod should be running and should have the labels from the manifest.

## Common Errors and Fixes

### YAML indentation error

Error example:

```text
error: error parsing ... did not find expected key
```

Fix:

```bash
kubectl apply -f day-1/manifests/pod.yaml
```

Check that indentation uses spaces and that nested fields line up correctly.

### Pod already exists

If the Pod already exists, `kubectl apply` may update it or report no changes.

To recreate it:

```bash
kubectl delete -f day-1/manifests/pod.yaml
kubectl apply -f day-1/manifests/pod.yaml
```

### Port 8080 is already in use

Use another local port:

```bash
kubectl port-forward pod/nginx-yaml 8081:80
curl http://localhost:8081
```

## Cleanup Commands

```bash
kubectl delete -f day-1/manifests/pod.yaml
kubectl get pods
```
