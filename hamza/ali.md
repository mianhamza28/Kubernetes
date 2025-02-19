# Ambassador Pod in Kubernetes 🚢

## What is an Ambassador Container? 🤔

The **ambassador container** is a common pattern in Kubernetes and distributed systems. It acts as a proxy to provide additional functionality without modifying the main application.

## Key Features 🌟

An ambassador container is a **sidecar container** that runs alongside the main application container in the same Pod, handling:

- 🔍 Service discovery
- ⚖️ Load balancing
- 🔄 Protocol translation
- 🛣️ Traffic routing
- 📊 Logging and monitoring

## Why Use an Ambassador Container? 🎯

### 1. Decoupling Application Logic from Networking 🔌
- Main application container stays focused on business logic
- Ambassador handles networking concerns:
  - Service discovery
  - Load balancing
  - Request retries

## Example Configurations 📝

### Basic Nginx Ambassador Pod

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: ambassador-nginx
  labels:
    app: ambassador-pod
spec:
  containers:
  - name: nginx
    image: nginx:stable
    ports:
    - containerPort: 80
```

### Ambassador Service

```yaml
apiVersion: v1
kind: Service
metadata:
  name: ambassador-service
spec:
  selector:
    app: ambassador-pod
  ports:
  - protocol: TCP
    port: 8081
    targetPort: 80
```

### Ambassador Pod with HAProxy 🔀

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: ambassador-pod
spec:
  containers:
  - name: main-container
    image: radial/busyboxplus:curl
    command: ["sh", "-c", "while true; do curl localhost:8080; sleep 5; done"]
  - name: ambassador-container
    image: haproxy:2.4
    ports:
    - containerPort: 8080
    volumeMounts:
    - name: haproxy-config
      mountPath: /usr/local/etc/haproxy/haproxy.cfg
      subPath: haproxy.cfg
  volumes:
  - name: haproxy-config
    configMap:
      name: haproxy-config
```

### HAProxy ConfigMap ⚙️

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: haproxy-config
data:
  haproxy.cfg: |
    frontend ambassador
      bind *:8080
      default_backend ambassador_service_svc
    backend ambassador_service_svc
      server svc ambassador-service:8081
```

## Useful Commands 💻

Check Services:

```bash
kubectl get svc
```

Apply Configuration:

```bash
kubectl apply -f <file.yaml>
```

View Resources:

```bash
kubectl get po,svc
```

Check Service Details:

```bash
kubectl describe svc ambassador-service
```

View Logs:

```bash
kubectl logs ambassador-pod -c main-container
```

View ConfigMaps:

```bash
kubectl get configmap
```

---

This `README.md` provides an overview of the Ambassador Pod pattern in Kubernetes, including its key features, benefits, example configurations, and useful commands. Use this guide to understand and implement ambassador containers in your Kubernetes deployments.
