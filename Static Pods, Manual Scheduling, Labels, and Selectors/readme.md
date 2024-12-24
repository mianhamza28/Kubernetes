#  static pods, manual scheduling, labels, and selectors in Kubernetes 📘🚀




Welcome to the quick reference guide for our Kubernetes Labels, Selectors, and Static Pods video! This guide will be handy as you dive deeper into Kubernetes, especially if you're starting. Let's explore these concepts in detail:

---

## 📌 Labels and Selectors in Kubernetes

### Labels 🏷️
Labels are key-value pairs attached to Kubernetes objects like pods, services, and deployments. They help organize and group resources based on criteria that make sense to you.

**Examples of Labels:**
- `environment: production`
- `type: backend`
- `tier: frontend`
- `application: my-app`

### Selectors 🔍
Selectors filter Kubernetes objects based on their labels. This is incredibly useful for querying and managing a subset of objects that meet specific criteria.

**Common Usage:**
- **Pods**: `kubectl get pods --selector app=my-app`
- **Deployments**: Used to filter the pods managed by the deployment.
- **Services**: Filter the pods to which the service routes traffic.

### Labels vs. Namespaces 🌍
- **Labels**: Organize resources within the same or across namespaces.
- **Namespaces**: Provide a way to isolate resources from each other within a cluster.

### Annotations 📝
Annotations are similar to labels but attach non-identifying metadata to objects. For example, recording the release version of an application for information purposes or last applied configuration details etc.

---

## 🛠️ Static Pods

Static Pods are special types of pods managed directly by the `kubelet` on each node rather than through the Kubernetes API server.

### Key Characteristics of Static Pods:
- **Not Managed by the Scheduler**: Unlike deployments or replicasets, the Kubernetes scheduler does not manage static pods.
- **Defined on the Node**: Configuration files for static pods are placed directly on the node's file system, and the `kubelet` watches these files.
- **Some examples of static pods are:** ApiServer, Kube-scheduler, controller-manager, ETCD etc
  
### Managing Static Pods:
1. **SSH into the Node**: You will gain access to the node where the static pod is defined.(Mostly the control plane node)
2. **Modify the YAML File**: Edit or create the YAML configuration file for the static pod.
3. **Remove the Scheduler YAML**: To stop the pod, you must remove or modify the corresponding file directly on the node.
4. **Default location**": is usually `/etc/kubernetes/manifests/`; you can place the pod YAML in the directory, and Kubelet will pick it for scheduling.

## 🧭 Manual Pod Scheduling

Manual scheduling in Kubernetes involves assigning a pod to a specific node rather than letting the scheduler decide.

### Key Points:
- **`nodeName` Field**: Use this field in the pod specification to specify the node where the pod should run.
- **No Scheduler Involvement**: When `nodeName` is specified, the scheduler bypasses the pod, and it’s directly assigned to the given node.

### Knowledge Reference

```
k get po -n kube-system | grep schedular
docker exec -it cka-cluster3-control-plane bash
k run nginx --image=nginx
k get po
k describe pod/nginx
```


### Modify Nginx Pod

```
docker exec -it cka-cluster3-control-plane bash
k delete pod nginx
k run nginx --image=nginx -o yaml > nginx.yaml
k delete pod nginx
```

#nginx.yaml

```
apiVersion: v1
kind: Pod
metadata:
  labels:
    run: nginx
  name: nginx
  namespace: default
spec:
  containers:
  - image: nginx
    name: nginx
  nodeName: cka-cluster3-worker
```

```
docker exec -it cka-cluster3-control-plane bash
```


# Working with Static Pods

```
cd /etc/kubernetes/manifests/
ls -lrt
mv kube-scheduler.yaml /tmp
```

# Second Terminal

```
k apply -f nginx.yaml
k get pods
k get po -o wide
```

# First Terminal

```
mv kube-scheduler.yaml /tmp
ls
```
## Redis Example

# redis.yaml:

```
apiVersion: v1
kind: Pod
metadata:
  labels:
    run: redis
    tier: backend
  name: redis
  namespace: default
spec:
  containers:
  - image: redis
    name: redis-container
```


```
k apply -f redis.yaml
k get pods
k get pod --selector tier=backend
```



