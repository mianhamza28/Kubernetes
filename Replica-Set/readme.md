# Lecture 12: Sidecar Pod 🚀

A **sidecar pattern** is used when you want to enhance or extend the functionality of a main container without modifying it. 💯

---

## Why Use Sidecars? 🤔

### ✨ Separation of Concerns

- **Main container**: Focuses on its core application.
- **Sidecar**: Handles supporting functions independently.

---

## Common Use Cases 💠

1. **Logging**: Collecting and forwarding logs from the main container.
2. **Monitoring**: Gathering metrics and telemetry data.
3. **Proxy/Ambassador**: Managing network traffic or API calls.
4. **Configuration Sync**: Updating configurations without restarting the main container.

---

## Benefits 🌟

- **Modularity**: Easy to add/remove functionality.
- **Maintainability**: Update sidecar without touching the main application.
- **Reusability**: Same sidecar can be used with different main containers.

---

### Example YAML: Sidecar Pod Configuration 📝

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: sidecar-pod
spec:
  containers:
  - name: main-container
    image: busybox:stable
    command: ["sh", "-c", "echo 'Hello from busybox' > /output-dir/data.txt ; while true; do sleep 5; done"]
    volumeMounts:
    - name: shared-volumes
      mountPath: /output-dir

  - name: side-car
    image: busybox:stable
    command: ["sh", "-c", "while true; do cat /input-dir/data.txt; sleep 5; done"]
    volumeMounts:
    - name: shared-volumes
      mountPath: /input-dir

  volumes:
  - name: shared-volumes
    emptyDir: {}
```

---

## Useful Commands 🖥️

### Check Nodes:

```bash
kubectl get nodes
```

### Describe Pod:

```bash
kubectl describe pod sidecar-pod
```

### Check Logs:

```bash
kubectl logs sidecar-pod -c side-car
kubectl logs <pod-name> -c <container-name>
```

---

### If Configuration Changes are Made: 🔄

```bash
kubectl replace -f <pod.yaml>
```

### Additional Command:

```bash
kubectl describe pod sidecar-pod | grep -i container
```

---

💡 **Tip**: Sidecars are a powerful way to decouple functionalities in Kubernetes pods, making your architecture more scalable and maintainable!


