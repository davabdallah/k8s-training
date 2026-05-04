# Lab 04 - Service and kube-proxy Flow

## Goal

Understand how a Service selects Pods, creates endpoints, and relies on kube-proxy to route traffic inside the cluster.


## Step-by-Step Commands

Create the Deployment and Service:

```bash
kubectl apply -f day-2/manifests/deployment.yaml
kubectl apply -f day-2/manifests/service.yaml
kubectl rollout status deployment/demo-web
```

View the Service:

```bash
kubectl get service demo-web -o wide
kubectl describe service demo-web
```

View the Pods and their labels:

```bash
kubectl get pods -l app=demo-web --show-labels -o wide
```

View the endpoints created for the Service:

```bash
kubectl get endpoints demo-web
```

View EndpointSlices, the modern endpoint tracking resource:

```bash
kubectl get endpointslices -l kubernetes.io/service-name=demo-web
```

Find kube-proxy:

```bash
kubectl get pods -n kube-system -l k8s-app=kube-proxy -o wide
```

View recent kube-proxy logs:

```bash
kubectl logs -n kube-system -l k8s-app=kube-proxy --tail=20
```

Create a client Pod:

```bash
kubectl run network-test --image=busybox:1.36 --restart=Never -- sleep 3600
kubectl wait --for=condition=Ready pod/network-test --timeout=120s
```

Send requests to the Service:

```bash
kubectl exec network-test -- wget -qO- http://demo-web
kubectl exec network-test -- wget -qO- http://demo-web
kubectl exec network-test -- wget -qO- http://demo-web
```

## Expected Output

For `kubectl describe service demo-web`, notice these fields:

```text
Type:              ClusterIP
IP Family Policy:  SingleStack
Port:              http  80/TCP
Endpoints:         10.244...:80,10.244...:80
```

For kube-proxy:

```text
NAMESPACE     NAME               READY   STATUS    RESTARTS   AGE
kube-system   kube-proxy-...      1/1     Running   0          ...
```

For the Service request:

```text
Welcome to nginx!
```

## Validation Commands

```bash
kubectl get service demo-web
kubectl get endpoints demo-web
kubectl get pods -l app=demo-web --show-labels
kubectl get pods -n kube-system -l k8s-app=kube-proxy
kubectl exec network-test -- wget -qO- http://demo-web
```

The Service selector should match the Pod labels, endpoints should exist, kube-proxy should be running, and requests should succeed.

## Common Errors and Fixes

### Service has no endpoints

The selector probably does not match any Pod labels.

Check:

```bash
kubectl describe service demo-web
kubectl get pods --show-labels
```

Fix the selector in `day-2/manifests/service.yaml`, then apply again:

```bash
kubectl apply -f day-2/manifests/service.yaml
```

### kube-proxy logs are different from the instructor machine

This is normal. The exact logs depend on Kubernetes version and network mode.

Validate kube-proxy is running:

```bash
kubectl get pods -n kube-system -l k8s-app=kube-proxy
```

### Service DNS does not resolve

Check CoreDNS:

```bash
kubectl get pods -n kube-system -l k8s-app=kube-dns
```

Then retry from the client Pod:

```bash
kubectl exec network-test -- nslookup demo-web
```

## Cleanup Commands

```bash
kubectl delete pod network-test --ignore-not-found
kubectl delete -f day-2/manifests/service.yaml
kubectl delete -f day-2/manifests/deployment.yaml
kubectl get service demo-web
kubectl get pods -l app=demo-web
```
