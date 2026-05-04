# kubectl Cheatsheet

## Context and Cluster

```bash
kubectl config current-context
kubectl config get-contexts
kubectl config use-context minikube
kubectl cluster-info
kubectl get nodes
kubectl get nodes -o wide
```

## Namespaces

```bash
kubectl get namespaces
kubectl create namespace dev
kubectl get pods --namespace=dev
kubectl get pods -A
kubectl delete namespace dev
```

## Pods

```bash
kubectl get pods
kubectl get pods -o wide
kubectl get pods --show-labels
kubectl describe pod <pod-name>
kubectl logs <pod-name>
kubectl exec <pod-name> -- <command>
kubectl delete pod <pod-name>
```

Create a simple Pod:

```bash
kubectl run nginx-demo --image=nginx:1.25-alpine --restart=Never --port=80
```

Wait for a Pod:

```bash
kubectl wait --for=condition=Ready pod/nginx-demo --timeout=120s
```

## Manifests

```bash
kubectl apply -f file.yaml
kubectl delete -f file.yaml
kubectl diff -f file.yaml
kubectl get -f file.yaml
```

## Deployments

```bash
kubectl get deployments
kubectl describe deployment <deployment-name>
kubectl apply -f deployment.yaml
kubectl rollout status deployment/<deployment-name>
kubectl rollout history deployment/<deployment-name>
kubectl rollout undo deployment/<deployment-name>
kubectl scale deployment <deployment-name> --replicas=3
kubectl set image deployment/<deployment-name> <container-name>=<image>
kubectl delete deployment <deployment-name>
```

## Services

```bash
kubectl get services
kubectl describe service <service-name>
kubectl get endpoints <service-name>
kubectl get endpointslices
kubectl expose deployment <deployment-name> --name=<service-name> --port=80 --target-port=80
kubectl delete service <service-name>
```

## ConfigMaps and Secrets

```bash
kubectl get configmaps
kubectl describe configmap <configmap-name>
kubectl get secrets
kubectl describe secret <secret-name>
kubectl set env deployment/<deployment-name> --from=configmap/<configmap-name>
kubectl set env deployment/<deployment-name> --from=secret/<secret-name>
```

Decode a training Secret value:

```bash
kubectl get secret <secret-name> -o jsonpath='{.data.KEY}' | base64 --decode
```

## Ingress

```bash
minikube addons enable ingress
kubectl get pods -n ingress-nginx
kubectl get ingress
kubectl describe ingress <ingress-name>
MINIKUBE_IP=$(minikube ip)
curl --resolve example.local:80:${MINIKUBE_IP} http://example.local/
```

## Storage

```bash
kubectl get storageclass
kubectl get pvc
kubectl describe pvc <pvc-name>
kubectl get pv
kubectl delete pvc <pvc-name>
```

## Events and Debugging

```bash
kubectl get events --sort-by=.lastTimestamp
kubectl describe pod <pod-name>
kubectl logs <pod-name>
kubectl logs <pod-name> --previous
kubectl get pods -o wide
```
