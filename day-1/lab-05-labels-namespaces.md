# Lab 05 - Labels and Namespaces

## Goal

Use namespaces to separate resources and labels to select groups of Pods.

## Estimated Time

60 minutes

## Prerequisites

- Minikube is running.
- kubectl context is set to `minikube`.
- You understand basic Pod creation.

## Step-by-Step Commands

Create two namespaces:

```bash
kubectl create namespace dev
kubectl create namespace test
```

Create one Pod in each namespace:

```bash
kubectl run api-dev --image=nginx:1.25-alpine --restart=Never --namespace=dev --labels=app=api,env=dev
kubectl run api-test --image=nginx:1.25-alpine --restart=Never --namespace=test --labels=app=api,env=test
```

List Pods in each namespace:

```bash
kubectl get pods --namespace=dev
kubectl get pods --namespace=test
```

List Pods with labels:

```bash
kubectl get pods --namespace=dev --show-labels
kubectl get pods --namespace=test --show-labels
```

Select Pods by label:

```bash
kubectl get pods --namespace=dev -l app=api
kubectl get pods --namespace=test -l env=test
```

Add a new label to the dev Pod:

```bash
kubectl label pod api-dev --namespace=dev owner=student
```

Filter by the new label:

```bash
kubectl get pods --namespace=dev -l owner=student --show-labels
```

View all namespaces:

```bash
kubectl get namespaces
```

## Expected Output

For namespace creation:

```text
namespace/dev created
namespace/test created
```

For `kubectl get pods --namespace=dev --show-labels`:

```text
NAME      READY   STATUS    RESTARTS   AGE   LABELS
api-dev   1/1     Running   0          ...   app=api,env=dev
```

After adding the owner label:

```text
pod/api-dev labeled
```

## Validation Commands

```bash
kubectl get pods --namespace=dev --show-labels
kubectl get pods --namespace=test --show-labels
kubectl get pods --all-namespaces -l app=api
```

You should see one `app=api` Pod in `dev` and one in `test`.

## Common Errors and Fixes

### Namespace not found

Check namespaces:

```bash
kubectl get namespaces
```

Create the missing namespace:

```bash
kubectl create namespace dev
```

### Label selector returns no resources

Check the actual labels:

```bash
kubectl get pods --namespace=dev --show-labels
```

Then retry with the correct label key and value.

### Label already exists

If you want to change an existing label, use `--overwrite`:

```bash
kubectl label pod api-dev --namespace=dev owner=student --overwrite
```

## Cleanup Commands

Delete the namespaces and everything inside them:

```bash
kubectl delete namespace dev test
```

Verify cleanup:

```bash
kubectl get namespaces
kubectl get pods --all-namespaces -l app=api
```
