# Lab 05 - ConfigMap and Secret

## Goal

Create a ConfigMap and a Secret, inject them into a Deployment as environment variables, and inspect the result.

## Step-by-Step Commands

Open the manifests:

```bash
cat day-2/manifests/configmap.yaml
cat day-2/manifests/secret.yaml
```

Create the ConfigMap and Secret:

```bash
kubectl apply -f day-2/manifests/configmap.yaml
kubectl apply -f day-2/manifests/secret.yaml
```

Inspect them:

```bash
kubectl get configmap app-config
kubectl describe configmap app-config
kubectl get secret app-secret
kubectl describe secret app-secret
```

Create a simple Deployment that sleeps:

```bash
kubectl create deployment config-secret-demo --image=busybox:1.36 -- sleep 3600
```

Inject ConfigMap values as environment variables:

```bash
kubectl set env deployment/config-secret-demo --from=configmap/app-config
```

Inject Secret values as environment variables:

```bash
kubectl set env deployment/config-secret-demo --from=secret/app-secret
```

Wait for the Deployment:

```bash
kubectl rollout status deployment/config-secret-demo
```

Store the Pod name:

```bash
POD_NAME=$(kubectl get pods -l app=config-secret-demo -o jsonpath='{.items[0].metadata.name}')
```

Read environment variables from inside the container:

```bash
kubectl exec "$POD_NAME" -- printenv APP_MESSAGE
kubectl exec "$POD_NAME" -- printenv APP_MODE
kubectl exec "$POD_NAME" -- printenv API_KEY
```

View the Secret as YAML:

```bash
kubectl get secret app-secret -o yaml
```

## Expected Output

For ConfigMap creation:

```text
configmap/app-config created
```

For Secret creation:

```text
secret/app-secret created
```

For `printenv APP_MESSAGE`:

```text
Hello from Kubernetes ConfigMap
```

For `printenv APP_MODE`:

```text
training
```

For `printenv API_KEY`:

```text
training-api-key
```

In YAML output, Secret values are base64 encoded.

## Validation Commands

```bash
kubectl get configmap app-config
kubectl get secret app-secret
kubectl get deployment config-secret-demo
kubectl exec "$POD_NAME" -- printenv APP_MESSAGE
kubectl exec "$POD_NAME" -- printenv API_KEY
```

The variables should be visible inside the running container.

## Common Errors and Fixes

### POD_NAME is empty

Check the Deployment and Pods:

```bash
kubectl get deployment config-secret-demo
kubectl get pods -l app=config-secret-demo
```

Set `POD_NAME` again:

```bash
POD_NAME=$(kubectl get pods -l app=config-secret-demo -o jsonpath='{.items[0].metadata.name}')
```

### Secret value looks unreadable in YAML

Kubernetes stores Secret data as base64 encoded strings. Decode one value for training only:

```bash
kubectl get secret app-secret -o jsonpath='{.data.API_KEY}' | base64 --decode
```

### Environment variable is missing

Restart the Deployment after setting environment variables:

```bash
kubectl rollout restart deployment/config-secret-demo
kubectl rollout status deployment/config-secret-demo
```

## Cleanup Commands

```bash
kubectl delete deployment config-secret-demo --ignore-not-found
kubectl delete -f day-2/manifests/configmap.yaml
kubectl delete -f day-2/manifests/secret.yaml
kubectl get configmap app-config
kubectl get secret app-secret
```
