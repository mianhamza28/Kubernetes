---
title: "Liveness-Readiness Probe"
seoTitle: "Liveness-Readiness Probe"
seoDescription: " Readiness and Liveness Probes are critical mechanisms for ensuring application reliability and availability."
datePublished: Tue Mar 25 2025 17:36:34 GMT+0000 (Coordinated Universal Time)
cuid: cm8os2bqf000g09l7bt96clvg
slug: liveness-readiness-probe
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1742923887341/d00d050a-f347-4ead-aabc-15ad996093ab.jpeg
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1742923903012/13675c29-d3ed-4b65-847a-00eac99754e3.jpeg

---

In Kubernetes, **Readiness** and **Liveness Probes** are critical mechanisms for ensuring application reliability and availability. Here's a structured explanation of their purposes:

### 1\. **Readiness Probe**

* **Purpose**: Determines if a pod is ready to handle traffic.
    
* **Why Use It?**
    
    * **Traffic Control**: Kubernetes uses this probe to decide when to add a pod to a Service’s endpoint list. If the probe fails, the pod is temporarily removed from load balancers, preventing requests from being sent to an unprepared container (e.g., during startup or maintenance).
        
    * **Smooth Deployments**: Ensures new pods are fully initialized before replacing old ones during rolling updates, avoiding downtime.
        
* **Example Use Case**: A pod running a web server might need to load configuration files or connect to a database before it can serve requests. The readiness probe delays traffic until these tasks complete.
    

### 2\. **Liveness Probe**

* **Purpose**: Checks if a pod is still running correctly.
    
* **Why Use It?**
    
    * **Self-Healing**: If the probe fails, Kubernetes restarts the pod to recover from issues like deadlocks, crashes, or hung processes.
        
    * **Fault Tolerance**: Ensures long-running apps remain healthy, even if they develop runtime issues not caught by crash detection (e.g., memory leaks).
        
* **Example Use Case**: An app that becomes unresponsive due to a bug stops responding to the liveness check. Kubernetes kills the pod and starts a new one.
    

---

### Key Differences

| **Aspect** | **Readiness Probe** | **Liveness Probe** |
| --- | --- | --- |
| **Primary Role** | Controls traffic flow to the pod. | Ensures pod health by triggering restarts. |
| **Effect of Failure** | Pod is removed from Service endpoints. | Pod is terminated and recreated. |
| **Typical Use Cases** | Startup initialization, dependency checks. | Crashes, deadlocks, unresponsive processes. |

---

### Configuration Tips

* **Probe Types**: Use HTTP (e.g., `/health` endpoint), TCP (port checks), or Exec (custom commands).
    
* **Timing**: Set appropriate `initialDelaySeconds` to avoid premature failures during app startup.
    
* **Thresholds**: Adjust `failureThreshold` and `periodSeconds` to balance sensitivity and stability.
    

By combining both probes, Kubernetes optimizes traffic routing and automates recovery, ensuring resilient and efficient application management.

Here's the corrected version with explanations and improvements:

**1\. YAML Files & Workflow**

```yaml
# web.yaml (Development Pod)
apiVersion: v1
kind: Pod
metadata:
  name: simple-website
  namespace: default
  labels:
    environment: development
    app: web-app              # Common label for service selector
spec:
  containers:
  - name: web-app
    image: kodekloud/webapp-delayed-start
    ports:
    - containerPort: 8080
    livenessProbe:
      httpGet:
        path: /live
        port: 8080
    readinessProbe:           # Added for completeness
      httpGet:
        path: /ready
        port: 8080
```

```yaml
# prod.yaml (Production Pod)
apiVersion: v1
kind: Pod
metadata:
  name: production-website
  namespace: default
  labels:
    environment: production
    app: web-app              # Common label for service selector
spec:
  containers:
  - name: production-website
    image: kodekloud/webapp-delayed-start
    ports:
    - containerPort: 8080
    livenessProbe:
      httpGet:
        path: /live
        port: 8080
    readinessProbe:
      httpGet:
        path: /ready
        port: 8080
```

```yaml
# svc.yaml (Service)
apiVersion: v1
kind: Service
metadata:
  name: web-app-service
  namespace: default
spec:
  type: NodePort
  selector:
    app: web-app             # Selects both pods
  ports:
    - protocol: TCP
      port: 80
      targetPort: 8080
      nodePort: 30010
```

**3\. Key Corrections Made:**

1. Fixed indentation for probes
    
2. Added missing `readinessProbe` to development pod
    
3. Standardized labels (`app: web-app`) for service selector
    
4. Removed conflicting `tier: production` label
    
5. Consolidated to single service instead of multiple
    
6. Added proper namespace declarations
    
7. Fixed NodePort configuration (30010 instead of 30080)
    
8. Removed deprecated `command: ["sleep"]` from prod pod
    

**4\. Deployment Commands:**

```bash
kubectl apply -f web.yaml
kubectl apply -f prod.yaml
kubectl apply -f svc.yaml

# Verify endpoints
kubectl get endpoints web-app-service
```

**5\. Testing Flow:**

1. Access via browser: `<Control-Plane-IP>:30010`
    
2. Check traffic distribution: `kubectl logs -f <pod-name>`
    
3. Force failure: `kubectl exec <pod> -- rm /app/health`
    
4. Watch recovery: `kubectl get pods -w`
    

**Important Notes:**

1. Ensure your application actually has /live and /ready endpoints
    
2. Add probe timing parameters for production:
    

```yaml
initialDelaySeconds: 5
periodSeconds: 10
failureThreshold: 3
```

3. Consider using ClusterIP with Ingress instead of NodePort for production
    
4. Use Deployment instead of Pod directly for better management
    

This configuration will now:

* Route traffic to both pods through single service
    
* Automatically restart unhealthy containers
    
* Prevent traffic to pods during startup
    
* Provide clear environment separation (dev/prod)