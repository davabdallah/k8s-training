# Troubleshooting Cheatsheet

## First Checks

```bash
minikube status
kubectl config current-context
kubectl get nodes
kubectl get pods -A
```

Expected basics:

- Minikube is running.
- Current context is `minikube`.
- Node is `Ready`.
- System Pods are running.

## Pod Status Guide

| Status | Meaning | First Command |
| --- | --- | --- |
| Pending | Pod has not been scheduled or volume is not ready | `kubectl describe pod <pod-name>` |
| ContainerCreating | Container is being created | `kubectl describe pod <pod-name>` |
| Running | Container is running | `kubectl logs <pod-name>` |
| Completed | Container finished successfully | `kubectl logs <pod-name>` |
| CrashLoopBackOff | Container starts and then crashes repeatedly | `kubectl logs <pod-name> --previous` |
| ImagePullBackOff | Kubernetes cannot pull the image | `kubectl describe pod <pod-name>` |
| ErrImagePull | Image pull failed recently | `kubectl describe pod <pod-name>` |

## Debug a Pod

```bash
kubectl get pods
kubectl describe pod <pod-name>
kubectl logs <pod-name>
kubectl get events --sort-by=.lastTimestamp
```

For a crashing container:

```bash
kubectl logs <pod-name> --previous
```

For a running container:

```bash
kubectl exec <pod-name> -- sh
```

## Debug a Deployment

```bash
kubectl get deployment <deployment-name>
kubectl describe deployment <deployment-name>
kubectl rollout status deployment/<deployment-name>
kubectl rollout history deployment/<deployment-name>
kubectl get replicasets
kubectl get pods -l app=<label-value>
```

Rollback:

```bash
kubectl rollout undo deployment/<deployment-name>
```

## Debug a Service

```bash
kubectl get service <service-name>
kubectl describe service <service-name>
kubectl get endpoints <service-name>
kubectl get pods --show-labels
```

Common issue:

- Service selector does not match Pod labels.

Check selector:

```bash
kubectl describe service <service-name>
```

Check labels:

```bash
kubectl get pods --show-labels
```

## Debug Ingress

```bash
kubectl get pods -n ingress-nginx
kubectl get ingress
kubectl describe ingress <ingress-name>
kubectl get service
kubectl get endpoints
```

Test with a resolved host:

```bash
MINIKUBE_IP=$(minikube ip)
curl --resolve training.local:80:${MINIKUBE_IP} http://training.local/
```

Common issues:

- Ingress addon is not enabled.
- Host name in curl does not match the Ingress rule.
- Service has no endpoints.
- Path does not match the requested URL.

## Debug PVC Storage

```bash
kubectl get storageclass
kubectl get pvc
kubectl describe pvc <pvc-name>
kubectl get pod <pod-name>
kubectl describe pod <pod-name>
```

Common issues:

- PVC is `Pending`.
- No default storage class exists.
- Pod references the wrong PVC name.

## Clean Training Resources

Use carefully. This removes many resources from the current namespace:

```bash
kubectl delete ingress --all
kubectl delete deployment --all
kubectl delete service --all
kubectl delete configmap --all
kubectl delete secret --all
kubectl delete pvc --all
kubectl delete pod --all
```

Delete training namespaces:

```bash
kubectl delete namespace dev test --ignore-not-found
```
