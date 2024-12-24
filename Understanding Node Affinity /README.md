## Beyond Node Selectors: Introducing Affinity 🚀

Node Selectors are great for basic pod placement based on node labels. But what if you need more control over where your pods land? Enter **Node Affinity**! This feature offers advanced capabilities to fine-tune pod scheduling in your Kubernetes cluster.

---

## Node Affinity: The Powerhouse 🔥

Node Affinity lets you define complex rules for where your pods can be scheduled based on node labels. Think of it as creating a wishlist for your pod's ideal home!

### Key Features:
- **Flexibility**: Define precise conditions for pod placement.
- **Control**: Decide where your pods can and cannot go with greater granularity.
- **Adaptability**: Allow pods to stay on their nodes even if the labels change after scheduling.

---

## Properties in Node Affinity
- requiredDuringSchedulingIgnoredDuringExecution
- preferredDuringSchedulingIgnoredDuringExecution

## Required During Scheduling, Ignored During Execution 🛠️

This is the strictest type of Node Affinity. Here's how it works:

1. **Specify Node Labels**: Define a list of required node labels (e.g., `disktype=ssd`) in your pod spec.
2. **Exact Match Requirement**: The scheduler only places the pod on nodes with those exact labels.
3. **Execution Consistency**: Once scheduled, the pod remains on the node even if the label changes.

4. ## References
- [CKA 2024 Resources - Day 15](https://github.com/piyushsachdeva/CKA-2024/tree/main/Resources/Day15)
- [Kubernetes Documentation - Affinity and Anti-Affinity](https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/#affinity-and-anti-affinity)

## Lab Steps

1. Check and clean up existing pods:
```bash
k get po
k delete po redis nginx
```

2. Create Redis pod with Required Node Affinity:

```yaml
# redis.yaml
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
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
        - matchExpressions:
          - key: disktype
            operator: In
            values:
            - ssd
status: {}
```

3. Apply and verify the configuration:
```bash
k apply -f redis.yaml
k describe pod redis
k get nodes
k get pods -o wide
```

4. Label the worker node:
```bash
k label node cka-cluster3-worker disktype=ssd
k describe pod redis
k get po -o wide
```

5. Create Redis pod with Preferred Node Affinity:

```yaml
# redis1.yaml
apiVersion: v1
kind: Pod
metadata:
  labels:
    run: redis
  name: redis-new
spec:
  containers:
  - image: redis
    name: redis-new
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Always
  affinity:
    nodeAffinity:
      preferredDuringSchedulingIgnoredDuringExecution:
      - weight: 1
        preference:
          matchExpressions:
          - key: disktype
            operator: In
            values:
            - hdd
status: {}
```

6. Apply and test preferred affinity:
```bash
k apply -f redis1.yaml
k get po -o wide
```

7. Remove the label and verify pod status:
```bash
k label node cka-cluster3-worker disktype-
k get po
```
Note: Pod continues running even after label removal

8. Create Redis pod with Exists operator:

```yaml
# redis2.yaml
apiVersion: v1
kind: Pod
metadata:
  labels:
    run: redis
  name: redis-new
spec:
  containers:
  - image: redis
    name: redis-2
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Always
  affinity:
    nodeAffinity:
      preferredDuringSchedulingIgnoredDuringExecution:
      - weight: 1
        preference:
          matchExpressions:
          - key: disktype
            operator: Exists
status: {}
```

9. Final cleanup and application:
```bash
k label node cka-cluster3-worker disktype=
kubectl delete pod redis-new
kubectl apply -f redis2.yaml
```

## Key Concepts Demonstrated
- Required Node Affinity (`requiredDuringSchedulingIgnoredDuringExecution`)
- Preferred Node Affinity (`preferredDuringSchedulingIgnoredDuringExecution`)
- Node Label Management
- Different Affinity Operators (`In`, `Exists`)
- Pod Scheduling Behavior with Node Affinity
