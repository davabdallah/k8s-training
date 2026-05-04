# Lab 03 - Service ClusterIP

## Goal

Expose a Deployment inside the cluster using a ClusterIP Service and test it from another Pod.

## Estimated Time

60 minutes

## Prerequisites

- Minikube is running.
- kubectl context is set to `minikube`.
- You are in the repository root.

## Step-by-Step Commands

Create the Deployment:

```bash
kubectl apply -f day-2/manifests/deployment.yaml
kubectl rollout status deployment/demo-web
```

Create the ClusterIP Service:

```bash
kubectl apply -f day-2/manifests/service.yaml
```

Inspect the Service:

```bash
kubectl get service demo-web
kubectl describe service demo-web
```

Check the Service endpoints:

```bash
kubectl get endpoints demo-web
```

Create a temporary client Pod:

```bash
kubectl run network-test --image=busybox:1.36 --restart=Never -- sleep 3600
kubectl wait --for=condition=Ready pod/network-test --timeout=120s
```

Call the Service from inside the cluster:

```bash
kubectl exec network-test -- wget -qO- http://demo-web
```

Call the Service using its full DNS name:

```bash
kubectl exec network-test -- wget -qO- http://demo-web.default.svc.cluster.local
```

## Expected Output

For `kubectl get service demo-web`:

```text
NAME       TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)   AGE
demo-web   ClusterIP   ...             <none>        80/TCP    ...
```

For `kubectl get endpoints demo-web`:

```text
NAME       ENDPOINTS                         AGE
demo-web   10.244...:80,10.244...:80         ...
```

For the `wget` commands, the response should include:

```text
Welcome to nginx!
```

## Validation Commands

```bash
kubectl get deployment demo-web
kubectl get pods -l app=demo-web
kubectl get service demo-web
kubectl get endpoints demo-web
kubectl exec network-test -- wget -qO- http://demo-web
```

The Service should have endpoints and should return the nginx welcome page.

## Common Errors and Fixes

### Service has no endpoints

Check that the Service selector matches the Pod labels:

```bash
kubectl describe service demo-web
kubectl get pods -l app=demo-web --show-labels
kubectl get endpoints demo-web
```

### network-test Pod is not ready

Inspect it:

```bash
kubectl describe pod network-test
```

If needed, delete and recreate it:

```bash
kubectl delete pod network-test
kubectl run network-test --image=busybox:1.36 --restart=Never -- sleep 3600
```

### Cannot access the Service from your laptop browser

ClusterIP Services are only reachable inside the cluster. Use a client Pod, port-forwarding, NodePort, LoadBalancer, or Ingress depending on the use case.

## Cleanup Commands

```bash
kubectl delete pod network-test --ignore-not-found
kubectl delete -f day-2/manifests/service.yaml
kubectl delete -f day-2/manifests/deployment.yaml
kubectl get service demo-web
kubectl get pods -l app=demo-web
```
