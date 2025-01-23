---
title: "Kubernetes Rollouts Guide🚀"
datePublished: Thu Jan 23 2025 15:50:56 GMT+0000 (Coordinated Universal Time)
cuid: cm69ifisn000w09ifh40rauo4
slug: kubernetes-rollouts-guide
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1737647380057/3c1f688c-ca11-4981-9d3f-bbd4de5ccd0a.webp
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1737647591446/84c07efb-582e-4762-8b1c-6fe5dbf98d24.webp

---

## Key Features 🌟

### Zero-Downtime Updates 🔄

* Enables application updates without service interruption
    
* Gradually replaces old pods with new ones
    
* Maintains application availability during update
    

### Version Control 📝

* Manages different versions of your application
    
* Keeps track of deployment history
    
* Enables easy rollback if issues occur
    

### Traffic Management 🚦

* Controls how traffic is shifted to new versions
    
* Supports various deployment strategies:
    
    * Rolling updates (default)
        
    * Blue-green deployments
        
    * Canary releases
        

### Health Monitoring 🏥

* Monitors new pods during deployment
    
* Automatically stops rollout if issues detected
    
* Ensures system stability
    

### Resource Management ⚙️

* Controls resource allocation during updates
    
* Prevents system overload
    
* Maintains performance standards'
    

## Sample Deployment Configuration 📄

```bash
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp-deployment
  labels:
    app: myapp
    tier: frontend
spec:
  replicas: 5
  selector:
    matchLabels:
      app: alnafi-app
  template:
    metadata:
      labels:
        app: alnafi-app
    spec:
      containers:
      - name: nginx-container
        image: nginx
```

### Deployment Management

```bash

# Apply deployment with recording
kubectl apply -f <file.yaml> --record

# Check pod status
kubectl get pod

# Check rollout status
kubectl rollout status deployment/<deployment-name>

# View rollout history
kubectl rollout history deployment/<deployment-name>
```

### Update & Rollback

```bash
# Update container image
kubectl set image deployment myapp-deployment nginx-container=nginx:1.22-alpine

# Apply rolling update
kubectl apply -f rolling.yaml

# View specific revision history
kubectl rollout history deployment/<deployment-name> --revision=2

# Rollback to previous deployment
kubectl rollout undo deploy myapp-deployment

# View deployment history
kubectl rollout history deployment myapp-deployment

# Rollback to specific version
kubectl rollout undo deploy myapp-deployment --to-revision=3
```