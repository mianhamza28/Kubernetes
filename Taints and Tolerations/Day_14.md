Day 14/40 - Taints and Tolerations in Kubernetes



```bash
k  delete pods nginx redis
```
```bash
k get nodes
```
```bash
kubectl taint node cka-cluster3-worker gpu=true:NoSchedule
```
```bash
kubectl describe  node cka-cluster3-worker2 |grep -i taint
```
```bash
k  run nginx --image=nginx
```
```bash
k get pods
```
nginx pod is pending

```bash
k run redis --image=redis --dry-run=client -o yaml > redis.yaml
```
we changes this
```yaml
apiVersion: v1
```
```yaml
kind: Pod
```
```yaml
metadata:
```
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

```bash
k apply -f redis.yaml
```
```bash
k get pods
```
no redis pod is runing

```bash
k run nginx  --image=nginx --dry-run=client -o yaml > newnginx.yaml
```
vim newnginx.yaml
```yaml
apiVersion: v1
```
```yaml
kind: Pod
```
```yaml
metadata:
```
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

```bash
kubectl delete pod nginx
```
```bash
kubectl apply -f newnginx.yaml
```


```bash
k get pods -o wide
```


```bash
k get nodes
```

```bash
k label node cka-cluster3-worker gpu=false
```

```bash
k get nodes --show-labels
```

```bash
k get nodes --show-labels
```



