# Lab 01 - Ingress

## Goal

Enable the Minikube Ingress addon and route HTTP traffic to a Kubernetes Service.


## Step-by-Step Commands

Enable the Ingress addon:

```bash
minikube addons enable ingress
```

Wait for the Ingress controller Pods:

```bash
kubectl get pods -n ingress-nginx
```

Create the demo Deployment and Service:

```bash
kubectl apply -f day-2/manifests/deployment.yaml
kubectl apply -f day-2/manifests/service.yaml
kubectl rollout status deployment/demo-web
```

Apply the Ingress:

```bash
kubectl apply -f day-3/manifests/ingress.yaml
```

Inspect the Ingress:

```bash
kubectl get ingress
kubectl describe ingress demo-web-ingress
```

Get the Minikube IP:

```bash
MINIKUBE_IP=$(minikube ip)
echo "$MINIKUBE_IP"
```

Send a request using the Ingress host:

```bash
curl --resolve demo.local:80:${MINIKUBE_IP} http://demo.local/
```

## Expected Output

For the Ingress controller:

```text
NAMESPACE       NAME                                        READY   STATUS    RESTARTS   AGE
ingress-nginx   ingress-nginx-controller-...                 1/1     Running   0          ...
```

For `kubectl get ingress`:

```text
NAME               CLASS   HOSTS        ADDRESS        PORTS   AGE
demo-web-ingress   nginx   demo.local   ...            80      ...
```

For the curl request, the response should include:

```text
Welcome to nginx!
```

## Validation Commands

```bash
kubectl get pods -n ingress-nginx
kubectl get service demo-web
kubectl get endpoints demo-web
kubectl get ingress demo-web-ingress
curl --resolve demo.local:80:${MINIKUBE_IP} http://demo.local/
```

Ingress is working when the curl request returns the nginx page.

## Common Errors and Fixes

### Ingress controller Pod is not ready

Wait and check again:

```bash
kubectl get pods -n ingress-nginx
```

If it does not appear, enable the addon again:

```bash
minikube addons enable ingress
```

### curl returns 404

Check the host and path:

```bash
kubectl describe ingress demo-web-ingress
```

Make sure the command uses `demo.local`:

```bash
curl --resolve demo.local:80:${MINIKUBE_IP} http://demo.local/
```

### Service has no endpoints

Check the Service selector and Pods:

```bash
kubectl get pods -l app=demo-web --show-labels
kubectl get endpoints demo-web
```

## Cleanup Commands

```bash
kubectl delete -f day-3/manifests/ingress.yaml
kubectl delete -f day-2/manifests/service.yaml
kubectl delete -f day-2/manifests/deployment.yaml
kubectl get ingress
kubectl get service demo-web
```
