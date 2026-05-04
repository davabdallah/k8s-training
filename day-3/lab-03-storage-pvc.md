# Lab 03 - PVC Storage

## Goal

Create a PersistentVolumeClaim, mount it into a Pod, write data, recreate the Pod, and confirm that the data remains.


## Step-by-Step Commands

Open the manifest:

```bash
cat day-3/manifests/pvc.yaml
```

Create the PVC and Pod:

```bash
kubectl apply -f day-3/manifests/pvc.yaml
```

Check the PVC:

```bash
kubectl get pvc
```

Check the Pod:

```bash
kubectl get pod storage-demo
kubectl wait --for=condition=Ready pod/storage-demo --timeout=120s
```

Write a file into the mounted volume:

```bash
kubectl exec storage-demo -- sh -c 'echo "hello from persistent storage" > /data/message.txt'
```

Read the file:

```bash
kubectl exec storage-demo -- cat /data/message.txt
```

Delete only the Pod:

```bash
kubectl delete pod storage-demo
```

Recreate the Pod using the same manifest:

```bash
kubectl apply -f day-3/manifests/pvc.yaml
kubectl wait --for=condition=Ready pod/storage-demo --timeout=120s
```

Read the file again:

```bash
kubectl exec storage-demo -- cat /data/message.txt
```

## Expected Output

For `kubectl get pvc`:

```text
NAME       STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
demo-pvc   Bound    pvc-...                                    1Gi        RWO            standard       ...
```

For reading the file:

```text
hello from persistent storage
```

The same message should appear after deleting and recreating the Pod.

## Validation Commands

```bash
kubectl get pvc demo-pvc
kubectl get pod storage-demo
kubectl exec storage-demo -- cat /data/message.txt
```

The PVC should be `Bound`, the Pod should be ready, and the file should still exist.

## Common Errors and Fixes

### PVC is Pending

Check the storage class:

```bash
kubectl get storageclass
kubectl describe pvc demo-pvc
```

Minikube normally provides a default storage class named `standard`.

### Pod is Pending

Describe the Pod:

```bash
kubectl describe pod storage-demo
```

Look for volume mount or PVC errors.

### File is missing after Pod recreation

Make sure you deleted only the Pod, not the PVC:

```bash
kubectl get pvc demo-pvc
```

If the PVC was deleted, recreate the lab from the beginning.

## Cleanup Commands

```bash
kubectl delete -f day-3/manifests/pvc.yaml
kubectl get pvc
kubectl get pod storage-demo
```
