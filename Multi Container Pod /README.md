

## Sample YAML used in the demo

```yaml
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
###  Commands

```
k create -f 8.yaml
k get pods
k logs myapp-pod
```
###  Specific Logs 


```
k logs myapp-pod  -c  init-myservice
```

### Deploy and Expose Nginx
```
k create deploy nginx-deploy --image nginx --port 80
k get deploy 
```


```
k expose  deploy nginx-deploy --name myservice   --port 80

k logs myapp-pod  -c  init-myservice
k exec -it myapp-pod -- printenv
```


