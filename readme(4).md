# Day 9/40 - Kubernetes Services Explained - ClusterIP vs NodePort vs Loadbalancer vs External ☸️


### Pre-requisite for Kind cluster
If you use a Kind cluster, you must perform the port mapping to expose the container port. Use the below config to create a new Kind cluster

```yaml
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
- role: control-plane
  extraPortMappings:
  - containerPort: 30001
    hostPort: 30001
- role: worker
- role: worker
```
###command to create new cluster 

``` kind create cluster --config kind.yaml --name cka-cluster```

### What is Service in Kubernetes

Different applications communicate with each other within Kubernetes using a service; it is also used to access applications outside the cluster.

There are 4 types of Services:
- ClusterIP(For Internal access)
- NodePort(To access the application on a particular port)
- LoadBalancer(To access the application on a domain name or IP address without using the port number)
- External (To use an external DNS for routing)

#### Sample YAML for ClusterIP

```yaml
apiVersion: v1
kind: Service
metadata:
  name: cluster-svc
  labels:
    env: demo
spec:
  ports:
  - port: 80
  selector:
    env: demo
```
#### Sample YAML for Nodeport

```yaml
apiVersion: v1
kind: Service
metadata:
  name: nodeport-svc
  labels:
    env: demo
spec:
  type: NodePort
  ports:
  - nodePort: 30001
    port: 80
    targetPort: 80
  selector:
    env: demo
```


### LoadBalancer
- Your loadbalancer service will act as nodeport if you are not using any managed cloud Kubernetes such as GKE,AKS,EKS etc. In a managed cloud environment, Kubernetes creates a load balancer within the cloud project, which redirects the traffic to the Kubernetes Loadbalancer service.


#### Sample YAML for Loadbalancer

```yaml
apiVersion: v1
kind: Service
metadata:
  name: lb-svc
  labels:
    env: demo
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    env: demo
```
