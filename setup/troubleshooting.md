# General Troubleshooting

This page lists common issues students may hit during the training.

## Minikube Does Not Start

Check Docker:

```bash
docker version
docker ps
```

Check Minikube:

```bash
minikube status
minikube logs
```

Common fixes:

- Start Docker Desktop or the Docker daemon.
- Free memory or CPU if the machine is overloaded.
- Delete and recreate the cluster:

```bash
minikube delete
minikube start --driver=docker
```

## kubectl Cannot Connect

Check the current context:

```bash
kubectl config current-context
kubectl config get-contexts
```

Use the Minikube context:

```bash
kubectl config use-context minikube
```

Validate:

```bash
kubectl get nodes
```

## Pod Stuck in Pending

Inspect the Pod:

```bash
kubectl describe pod <pod-name>
```

Look for:

- Insufficient CPU or memory
- Missing PersistentVolume
- Node selector or scheduling constraints

## Pod Stuck in ImagePullBackOff

Inspect the Pod:

```bash
kubectl describe pod <pod-name>
```

Common causes:

- Image name is wrong
- Image tag does not exist
- Network cannot reach the image registry
- Private image requires credentials

Fix the image and reapply the manifest:

```bash
kubectl apply -f <file-name>.yaml
```

## Service Has No Endpoints

Check the Service selector:

```bash
kubectl describe service <service-name>
kubectl get pods --show-labels
kubectl get endpoints <service-name>
```

Common cause:

- The Service selector does not match the Pod labels.

## Ingress Does Not Work

Check the Minikube Ingress addon:

```bash
minikube addons list
kubectl get pods -n ingress-nginx
```

Enable it:

```bash
minikube addons enable ingress
```

Check the Ingress:

```bash
kubectl describe ingress <ingress-name>
```

## Cleanup Everything from the Labs

Use these commands when you want to remove common training resources:

```bash
kubectl delete namespace dev test --ignore-not-found
kubectl delete deployment --all
kubectl delete service --all
kubectl delete ingress --all
kubectl delete configmap --all
kubectl delete secret --all
kubectl delete pvc --all
kubectl delete pod --all
```
