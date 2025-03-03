---
title: "Understanding Kubernetes Requests & Limits 🚀🔧"
seoTitle: "Kubernetes Requests & Limits"
seoDescription: "Requests: This is the minimum amount of resources a pod needs to operate smoothly
Limits: This is the maximum amount of resources a pod can use"
datePublished: Mon Mar 03 2025 09:29:45 GMT+0000 (Coordinated Universal Time)
cuid: cm7suzj9b000409juer2a8o8v
slug: understanding-kubernetes-requests-and-limits
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1740994015382/99573509-884e-4fa4-a94f-7f6956bb377e.png
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1740994007579/86a7eb75-023d-4b3b-a766-8ac65000662d.png
tags: kubernetes, k8s, container-orchestration, kubernetes-requests-and-limits

---

### 🏙️ What's the Deal with Requests & Limits?

Think of your Kubernetes cluster as a bustling city and pods as tenants in an apartment building. Each tenant (pod) requires specific resources like CPU and memory to function:

* **Requests**: This is the minimum amount of resources a pod needs to operate smoothly. Think of it as a guaranteed reservation for the pod.
    
* **Limits**: This is the maximum amount of resources a pod can use. It acts as a safety cap to prevent any pod from consuming more than its fair share and disrupting others.
    

  

### Why are Requests & Limits Important?

  

* **Resource Control**: By setting limits, you prevent a single pod from monopolizing resources, which can lead to issues like out-of-memory (OOM) kills or CPU starvation. ☠️ Why OOM can be a good thing? Because it kills the pod; otherwise, the container would have consumed all the memory and could kill the node. Killing the pod is a better option than killing a node itself.
    
* **Predictability**: Requests help the scheduler allocate resources efficiently and ensure pods have the necessary resources to run effectively.
    

  

1. **Exceeding Available Memory**: A pod requesting more memory than is available will be killed due to an OOM (Out of Memory) error.
    

```bash
apiVersion: v1
kind: Pod
metadata:
 name: memory-demo-2
 namespace: mem-example
spec:
 containers:
 - name: memory-demo-2-ctr
   image: polinux/stress
   resources:
     requests:
       memory: "50Mi"
     limits:
       memory: "100Mi"
   command: ["stress"]
   args: ["--vm", "1", "--vm-bytes", "250M", "--vm-hang", "1"]
```

2\. The Below pod will be scheduled

```bash
apiVersion: v1
kind: Pod
metadata:
  name: memory-demo
  namespace: mem-example
spec:
  containers:
  - name: memory-demo-ctr
    image: polinux/stress
    resources:
      requests:
        memory: "100Mi"
      limits:
        memory: "200Mi"
    command: ["stress"]
    args: ["--vm", "1", "--vm-bytes", "150M", "--vm-hang", "1"]
```