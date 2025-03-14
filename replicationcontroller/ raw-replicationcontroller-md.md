# ReplicationController

Let me explain why Replication Controllers are used in Kubernetes, though it's worth noting that they have largely been superseded by Deployments in modern Kubernetes applications.

Replication Controllers serve several important purposes:

1. **High Availability**
- Ensures that a specified number of pod replicas are running at all times
- If a pod fails, crashes, or is deleted, the RC automatically creates a new pod to maintain the desired count
- This helps prevent application downtime

2. **Load Balancing & Scaling**
- Distributes application load across multiple pod instances
- Allows horizontal scaling by increasing or decreasing the number of replicas
- Can handle increased traffic by running multiple copies of your application

3. **Rolling Updates**
- Enables basic rolling updates to your applications
- Can gradually replace old pods with new ones
- Though Deployments handle this much better with additional features

4. **Self-Healing**
- Continuously monitors the health of pods
- Automatically replaces unhealthy pods
- Reschedules pods if a node fails

However, it's important to note that Deployments are now the recommended way to manage pods in Kubernetes because they offer additional benefits like:
- Better rolling update mechanisms
- Rollback capabilities
- More flexible update strategies
- Version tracking

```yaml
apiVersion: v1
kind: ReplicationController
metadata:
  name: myapp-rc
  labels:
    app: frontend
    type: development
spec:
  replicas: 3
  selector:
    app: frontend
  template:
    metadata:
      labels:
        app: frontend
    spec:
      containers:
      - name: alpha
        image: nginx
```

```bash
k apply -f rc.yaml
k get pods
```

kubectl get replicationcontroller
```
NAME       DESIRED   CURRENT   READY   AGE
myapp-rc   3         3         3       31s
```

**if we make 3 replicas how we add more 2 replicas ?**

**Use kubectl scale Command**

Instead of editing the YAML file, you can use the kubectl scale command to quickly scale the number of replicas.

**For ReplicationController:**
```bash
kubectl scale replicationcontroller <rc-name> --replicas=5
```

**For Deployment:**
```bash
kubectl scale deployment <deployment-name> --replicas=5
```

kubectl get replicationcontroller
```
NAME       DESIRED   CURRENT   READY   AGE
myapp-rc   5         5         5       18m
```

**If We Delete replicationcontroller**

```
k delete replicationcontroller myapp-deployment
```
