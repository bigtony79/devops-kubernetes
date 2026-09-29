# Services - Connecting to applications inside the cluster

## Kubernetes Services

### Why Do We Need Services?

Pods are the smallest deployable units in Kubernetes, but they are **ephemeral** by nature.

### Challenges with Pods

* Each Pod receives its own IP address.
* Pod IPs change when Pods are:
  * Deleted
  * Recreated
  * Rescheduled to another node
* Applications cannot reliably communicate using Pod IPs directly.

Example:

```text
Frontend Pod --> Backend Pod (10.244.1.5)
```

If the Backend Pod is recreated:

```text
Frontend Pod --> Backend Pod (10.244.3.8)
```

The old IP becomes invalid.

## What is a Service?

A **Service** is an abstraction that provides a stable network endpoint for accessing a set of Pods.

Instead of communicating directly with Pod IP addresses, applications communicate through a Service.

### Benefits of Services

* Stable IP Address
* Stable DNS Name
* Load Balancing across Pods
* Service Discovery
* Loose Coupling between applications

Example:

```text
Frontend
     |
     |
     v
Backend Service
     |
 -------------------
 |        |        |
Pod A   Pod B   Pod C
```

The Service automatically routes traffic to healthy Pods.

## Viewing Pod IP Addresses

To see IP addresses assigned to Pods:

```bash
kubectl get pods -o wide
```

Example Output:

```text
NAME         READY   STATUS    IP
nginx-1      1/1     Running   10.244.0.5
nginx-2      1/1     Running   10.244.0.6
nginx-3      1/1     Running   10.244.0.7
```


Because these IPs can change, applications should generally connect through a Kubernetes Service.


***


## Service Types


Kubernetes provides several Service types:


* ClusterIP
* Headless Service
* NodePort
* LoadBalancer


***


## ClusterIP Service


### Default Service Type


If no type is specified, Kubernetes creates a **ClusterIP** Service.


```yaml
spec:
  type: ClusterIP
```


### Characteristics


* Internal-only access
* Accessible only within the cluster
* Provides a stable virtual IP
* Performs load balancing across Pods


Example:


```text
Frontend Pod
      |
      v
Backend Service (ClusterIP)
      |
 -------------------
 |        |        |
Pod A   Pod B   Pod C
```


### Use Cases


* Microservice communication
* Backend APIs
* Internal databases
* Internal application components


### Create a ClusterIP Service


```bash
kubectl expose deployment nginx \
  --port=80 \
  --target-port=80
```


View Services:


```bash
kubectl get svc
```


Example:


```text
NAME         TYPE        CLUSTER-IP
nginx        ClusterIP   10.96.150.22
```


***


## Service Discovery Using DNS


Every Service receives a DNS name.


Example:


```text
backend.default.svc.cluster.local
```


Most Pods can simply access:


```text
http://backend
```


Kubernetes DNS automatically resolves the Service name to the ClusterIP.


***


## Headless Service


A **Headless Service** does not receive a ClusterIP.


Instead of load balancing traffic, it returns the IP addresses of individual Pods.


### Configuration


```yaml
spec:
  clusterIP: None
```


***


## Why Use a Headless Service?


Sometimes clients need to communicate with a specific Pod instead of receiving traffic through a load balancer.


### Characteristics


* No virtual IP
* No load balancing
* Direct Pod-to-Pod communication
* DNS returns Pod IPs


***


## Common Use Cases


### Stateful Applications


Examples:


* MySQL
* PostgreSQL
* MongoDB
* Cassandra
* Kafka


These applications have replicas with different identities and responsibilities.


Example:


```text
mysql-0
mysql-1
mysql-2
```


Applications may need to communicate with a specific replica.


***


### StatefulSets


Headless Services are commonly used with StatefulSets.


Example DNS entries:


```text
mysql-0.mysql.default.svc.cluster.local
mysql-1.mysql.default.svc.cluster.local
mysql-2.mysql.default.svc.cluster.local
```


Each Pod has its own predictable hostname.


***


## NodePort Service


A NodePort Service exposes an application externally using a port on every cluster node.


```yaml
spec:
  type: NodePort
```


***


## How It Works


```text
Internet
    |
NodeIP:30080
    |
NodePort Service
    |
ClusterIP Service
    |
Application Pods
```


***


## Port Range


Default Kubernetes NodePort range:


```text
30000 - 32767
```


Example:


```yaml
ports:
- port: 80
  targetPort: 8080
  nodePort: 30080
```


Access:


```text
http://<Node-IP>:30080
```


***


## Characteristics


* External access to applications
* Available on every worker node
* Simple setup
* Useful for testing and labs


***


## Drawbacks


### Limited Security


The service is directly exposed through the node.


### Port Management


Must manage port assignments manually.


### Not Cloud Native


Provides no advanced traffic management.


### Difficult to Scale


As environments grow, managing many NodePorts becomes challenging.


***


## Recommended Usage


✅ Development


✅ Learning Kubernetes


✅ Small lab environments


❌ Production workloads


***


## LoadBalancer Service


A LoadBalancer Service exposes an application externally through a cloud provider's load balancer.


```yaml
spec:
  type: LoadBalancer
```


***


## How It Works


```text
Internet
    |
Cloud Load Balancer
    |
NodePort
    |
ClusterIP
    |
Application Pods
```


Kubernetes automatically provisions a cloud load balancer when running in:


* AWS
* Azure
* Google Cloud


***


## Characteristics


* Public IP Address
* External access
* Built-in load balancing
* Managed by cloud provider


***


## Example


```bash
kubectl get svc
```


Output:


```text
NAME         TYPE           EXTERNAL-IP
web-app      LoadBalancer   20.85.10.100
```


Applications can then be accessed through:


```text
http://20.85.10.100
```


***


## Ingress vs LoadBalancer


Many applications expose multiple services.


Without Ingress:


```text
LoadBalancer -> App1
LoadBalancer -> App2
LoadBalancer -> App3
```


This may require multiple external load balancers.


***


## Ingress


Ingress acts as a smart HTTP/HTTPS router.


```text
Internet
    |
Ingress Controller
    |
 -----------------
 |       |       |
App1   App2   App3
```


Example routing:


```text
app.company.com       -> App1
api.company.com       -> App2
admin.company.com     -> App3
```


***


## Benefits of Ingress


* Single entry point
* Host-based routing
* Path-based routing
* SSL/TLS termination
* Lower cloud costs
* Better traffic management


***


## Production Best Practices


### Use ClusterIP For


* Internal application communication
* Backend services
* Microservices


### Use Headless Services For


* StatefulSets
* Databases
* Kafka clusters
* Direct Pod access


### Use NodePort For


* Testing
* Learning
* Temporary environments


### Use LoadBalancer For


* Public-facing applications
* Cloud environments


### Use Ingress For


* Production web applications
* Multiple services
* HTTPS termination
* Centralized routing


***


## Key Takeaways


✅ Pods receive IP addresses, but Pod IPs are not permanent.


✅ Services provide a stable endpoint for accessing Pods.


✅ ClusterIP is the default Service type and is used for internal traffic.


✅ Headless Services (`clusterIP: None`) allow direct communication with specific Pods.


✅ NodePort exposes applications externally using ports `30000–32767` on every node.


✅ LoadBalancer creates a cloud-managed load balancer and automatically includes NodePort and ClusterIP functionality.


✅ In production environments, prefer **Ingress + LoadBalancer** instead of directly exposing applications through NodePort.


✅ Services enable Kubernetes applications to remain resilient even when Pods are recreated or moved within the cluster.
</details>