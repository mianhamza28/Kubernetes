Here’s an enhanced and corrected version of your Blue/Green deployment guide with clearer commands and structure:

---

# Blue/Green Deployment in Kubernetes 🌐

## What is Blue/Green Deployment? 🔵🟢

Blue/Green deployment minimizes downtime and risk by maintaining two identical environments:  
- **Blue**: Current production environment (live)  
- **Green**: New version (staging). Once validated, traffic switches from Blue to Green.

---

## Advantages ✅
- **Zero downtime** during deployment  
- **Instant rollback** to Blue if issues arise  
- **Safe testing** in production-like environment  

---

## Prerequisites ⚙️
- Kubernetes cluster (`kubectl` configured)
- Basic understanding of Deployments and Services

---

## Step 1: Deploy Blue Environment (v1)

### 1.1 Blue Deployment (v1)
**`blue-deployment.yaml`**
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: blue-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: my-app
      version: v1
  template:
    metadata:
      labels:
        app: my-app
        version: v1
    spec:
      containers:
      - name: nginx
        image: linuxacademycontent/ckad-nginx:1.0.0
        ports:
        - containerPort: 80
```

Apply configuration:
```bash
kubectl apply -f blue-deployment.yaml
```

### 1.2 Blue Service
**`blue-service.yaml`**
```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-app-service
spec:
  type: ClusterIP
  selector:
    app: my-app
    version: v1  # Points to Blue initially
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80
```

Create service:
```bash
kubectl apply -f blue-service.yaml
```

Verify:
```bash
kubectl get pods -l version=v1
kubectl get svc my-app-service
```

---

## Step 2: Deploy Green Environment (v2)

### 2.1 Green Deployment (v2)
**`green-deployment.yaml`**
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: green-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: my-app
      version: v2
  template:
    metadata:
      labels:
        app: my-app
        version: v2
    spec:
      containers:
      - name: nginx
        image: linuxacademycontent/ckad-nginx:canary
        ports:
        - containerPort: 80
```

Deploy Green:
```bash
kubectl apply -f green-deployment.yaml
```

---

## Step 3: Validate Green Environment 🧪

Check pods:
```bash
kubectl get pods -l version=v2
```

Test internally:
```bash
# Get test pod
kubectl run curl-test --image=radial/busyboxplus:curl -i --tty --rm

# Test Green deployment directly (not through service)
curl green-deployment-svc:80
```

---

## Step 4: Traffic Switch 🔀

Update service selector:
```bash
kubectl patch svc my-app-service -p '{"spec":{"selector":{"version":"v2"}}}'
```

**Alternative method** (edit service):
```bash
kubectl edit svc my-app-service
# Change selector: version: v1 → v2
```

---

## Step 5: Verify Traffic Shift 🔍

Check endpoints:
```bash
kubectl describe svc my-app-service | grep Endpoints
```

Test from worker node:
```bash
# Get service ClusterIP
SVC_IP=$(kubectl get svc my-app-service -o jsonpath='{.spec.clusterIP}')

# Access from pod
kubectl exec -it <blue-pod> -- curl -s $SVC_IP
```

---

## Rollback Procedure ⏮️

Revert service selector:
```bash
kubectl patch svc my-app-service -p '{"spec":{"selector":{"version":"v1"}}}'
```

Delete Green environment (if needed):
```bash
kubectl delete deployment green-deployment
```

---

## Key Commands Cheatsheet 📋

| Command | Description |
|---------|-------------|
| `kubectl get pods -l version=v1` | List Blue pods |
| `kubectl rollout status deployment/green-deployment` | Check Green deployment status |
| `kubectl get endpoints my-app-service` | Verify active endpoints |
| `kubectl logs <pod-name>` | Check container logs |

---

## Best Practices 💡
1. Use **readiness probes** for health checks
2. Implement **auto-scaling** for both environments
3. Use **versioned tags** in container images
4. Monitor metrics during switchover

---

This version provides:
- Consistent labeling (`version` instead of `environment`)
- Clear separation of Blue/Green environments
- Proper service selector updates
- Validation steps for each phase
- Rollback instructions
- Standardized kubectl commands without aliases
- Improved YAML structure with version labels
