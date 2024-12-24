# Day 14/40 - Taints and Tolerations in Kubernetes

Reference: [Kubernetes Documentation - Taints and Tolerations](https://kubernetes.io/docs/concepts/scheduling-eviction/taint-and-toleration/)

## Commands and Steps

1. Delete existing pods:
```bash
k delete pods nginx redis
```

2. Check nodes:
```bash
k get nodes
```

3. Add taint to worker node:
```bash
kubectl taint node cka-cluster3-worker gpu=true:NoSchedule
```

4. Check taints on worker2:
```bash
kubectl describe node cka-cluster3-worker2 | grep -i taint
```

5. Create nginx pod:
```bash
k run nginx --image=nginx
```

6. Check pods status:
```bash
k get pods
```

Note: nginx pod will be in pending state

7. Generate redis pod YAML:
```bash
k run redis --image=redis --dry-run=client -o yaml > redis.yaml
```

## Redis Pod Configuration

```yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: redis
  name: redis
spec:
  containers:
  - image: redis
    name: redis
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Always
  tolerations:
  - key: "key1"
    operator: "Equal"
    value: "value1"
    effect: "NoSchedule"
status: {}
```

8. Apply redis configuration:
```bash
k apply -f redis.yaml
```

9. Check pods status:
```bash
k get pods
```

Note: Redis pod is not running

## Nginx Pod Configuration with Node Selector

```yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: nginx
  name: nginx
spec:
  containers:
  - image: nginx
    name: nginx
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Always
  nodeSelector:
    gpu: "false"
status: {}
```

10. Apply new nginx configuration:
```bash
kubectl delete pod nginx
kubectl apply -f newnginx.yaml
```

11. Check pod details:
```bash
k get pods -o wide
```

12. Label the worker node:
```bash
k label node cka-cluster3-worker gpu=false
```

13. Verify node labels:
```bash
k get nodes --show-labels
```

This README documents the process of working with Kubernetes taints, tolerations, and node selectors. The example demonstrates how to:
- Apply taints to nodes
- Configure pods with tolerations
- Use node selectors for pod scheduling
- Label nodes for pod assignment
