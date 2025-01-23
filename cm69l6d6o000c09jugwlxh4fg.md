---
title: "Multi-container Pod"
datePublished: Thu Jan 23 2025 17:07:48 GMT+0000 (Coordinated Universal Time)
cuid: cm69l6d6o000c09jugwlxh4fg
slug: multi-container-pod
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1737651886231/05f34df4-97a5-41ec-a15a-cf98d3c321d9.png

---

A Pod that contains multiple containers that need to work together and share resources. All containers in a Pod are scheduled on the same node and share the same network namespace and storage volumes.

## Why Use Multi-container Pods? 🎯

### 1\. Resource Sharing 📦

* Containers share the same network namespace ([localhost](http://localhost))
    
* Can communicate via [localhost](http://localhost)
    
* Share the same IP address and port space
    
* Share the same storage volumes
    
* Co-located and co-scheduled
    

```bash
apiVersion: v1
kind: Pod
metadata:
  name: myapp-pod
  labels:
    app.kubernetes.io/name: MyApp
spec:
  containers:
  - name: myapp-container
    image: busybox:1.28
    env:
    - name: FIRSTNAME
      value: "Piyush"
    command: ['sh', '-c', 'echo The app is running! && sleep 3600']
  initContainers:
  - name: init-myservice
    image: busybox:1.28
    command: ['sh', '-c']
    args: ['until nslookup myservice.default.svc.cluster.local; do echo waiting for myservice; sleep 2; done']
  - name: init-mydb
    image: busybox:1.28
    command: ['sh', '-c']
    args: ['until nslookup mydb.default.svc.cluster.local; do echo waiting for mydb; sleep 2; done']
```

### Commands

```bash
k create -f 8.yaml
k get pods
k logs myapp-pod
```

### Specific Logs:

```bash
k logs myapp-pod  -c  init-myservice
```

### Deploy and Expose Nginx:

```bash
k create deploy nginx-deploy --image nginx --port 80
k get deploy 
```

```bash
k expose  deploy nginx-deploy --name myservice   --port 80

k logs myapp-pod  -c  init-myservice
k exec -it myapp-pod -- printenv
```