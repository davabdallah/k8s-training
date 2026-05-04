# Day 2 - Deployments and Services

Day 2 moves from single Pods to application management with Deployments and stable network access with Services.

## Learning Objectives

By the end of Day 2, students should be able to:

- Create and inspect Deployments.
- Scale applications up and down.
- Perform rolling updates.
- Roll back a failed update.
- Expose Pods inside the cluster using ClusterIP Services.
- Connect Services to Pods through selectors and endpoints.
- Explain the role of kube-proxy in Service traffic.
- Use ConfigMaps and Secrets.

## Agenda

Duration: 6 hours

| Time | Topic |
| --- | --- |
| 75 min | Deployments and ReplicaSets |
| 60 min | Scaling |
| 75 min | Rolling updates and rollback |
| 75 min | Services and ClusterIP |
| 45 min | Service selectors, endpoints, and kube-proxy |
| 60 min | ConfigMaps and Secrets |
| 30 min | Review and cleanup |

## Labs

- [Lab 01 - Deployment create and scale](lab-01-deployment-create-scale.md)
- [Lab 02 - Rolling update and rollback](lab-02-rolling-update-rollback.md)
- [Lab 03 - Service ClusterIP](lab-03-service-clusterip.md)
- [Lab 04 - Service and kube-proxy flow](lab-04-service-kube-proxy-flow.md)
- [Lab 05 - ConfigMap and Secret](lab-05-configmap-secret.md)
