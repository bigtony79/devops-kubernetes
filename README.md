# Module 10 : Container Orchestration with Kubernetes
This repository documents my hands‑on journey learning Kubernetes. It contains notes, references, and links to practical projects as I build deeper understanding of container orchestration, cluster operations, and cloud‑native best practices.

## Notes
[Introduction to Kubernetes](notes/Introduction.md)

[Main Kubernetes Components](notes/Main_Kubernetes_Components.md)

[Kubernetes Architecture](notes/Kubernetes_Architecture.md)

[Minikube and kubectl](notes/Minikube_and_kubectl.md)

[Main kubectl commands](notes/Main_kubectl_commands.md)

[YAML Configuration Files in Kubernetes](notes/YAML_Configuration_files_K8s.md)

[Namespaces in Kubernetes](notes/Namespaces_Kubernetes.md)





<details>
<summary>Services - Connecting to applications inside the cluster</summary>

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

***

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

***

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

<details>
<summary>Volumes - Persisting Application Data</summary>

## Why Volumes Matter

Containers are ephemeral by design. When a Pod is deleted or recreated, any data stored inside the container is lost. Kubernetes therefore requires external storage configuration for applications that need persistent data.

### Persistent Storage Requirements

* Data must not depend on the Pod lifecycle.
* Storage should remain available after Pod restarts or rescheduling.
* Storage should be accessible across cluster nodes.
* Data should survive node failures and cluster disruptions.
* Critical for stateful workloads such as databases.

***

## Local vs Remote Volumes

### Local Volumes

Storage resides on the node where the Pod runs.

**Examples**

* `emptyDir`
* `hostPath`
* ConfigMap volumes
* Secret volumes

**Use Cases**

* Temporary data
* Caching
* Configuration files
* Secrets management

**Limitations**

* Node-dependent
* Not highly available
* Risk of data loss if the node fails

***

### Remote Volumes

Storage exists outside the worker node and is accessed over the network.

**Examples**

* AWS EBS
* Azure Disk
* Azure Files
* Google Persistent Disk
* NFS
* Ceph

**Benefits**

* Survives Pod and node failures
* Supports high availability
* Suitable for production workloads

> **Best Practice:** Always use remote storage for databases and other stateful applications.

***

## Persistent Volume (PV)

A **Persistent Volume (PV)** is a cluster-level storage resource representing physical storage available to Kubernetes.

### Key Characteristics

* Created using YAML manifests
* Represents actual storage infrastructure
* Independent of Pod lifecycle
* Cluster-wide resource (not namespaced)
* Typically provisioned by cluster administrators

### Storage Backends

A PV can be backed by:

* Cloud block storage (AWS EBS, Azure Disk, GCP PD)
* NFS servers
* SAN/NAS systems
* Ceph clusters
* Local disks

**Architecture**

```text
Physical Storage
       │
       ▼
Persistent Volume (PV)
```

***

## Persistent Volume Claim (PVC)

A **Persistent Volume Claim (PVC)** is a request for storage made by an application.

Rather than directly consuming a PV, applications consume storage through a PVC.

### Key Characteristics

* Created using YAML
* Namespaced resource
* Requests specific storage requirements
* Binds to a matching PV

### Example Requests

* Storage size (10Gi, 50Gi, etc.)
* Access mode (ReadWriteOnce, ReadOnlyMany, ReadWriteMany)
* StorageClass

**Architecture**

```text
Pod
 │
 ▼
PVC
 │
 ▼
PV
```

***

## Storage Classes

A **StorageClass** enables dynamic storage provisioning.

Instead of administrators manually creating PVs, Kubernetes can automatically provision storage when requested by a PVC.

### Benefits

* Automated PV creation
* Reduced administrative effort
* Standardized storage provisioning
* Cloud-native scalability

### Defines

* Storage provider
* Provisioner
* Disk type
* Performance characteristics
* Replication and availability settings

***

## Dynamic Provisioning Workflow

```text
Pod
 │
 ▼
PVC
 │
 ▼
StorageClass
 │
 ▼
Dynamically Created PV
 │
 ▼
Storage Backend
```

### Process

1. Pod references a PVC.
2. PVC requests storage.
3. StorageClass provisions a matching PV.
4. PVC binds to the PV.
5. Pod mounts and consumes the storage.

***

## Kubernetes Storage Abstraction Layers

```text
Application
     │
     ▼
Pod
     │
     ▼
Persistent Volume Claim (PVC)
     │
     ▼
Persistent Volume (PV)
     │
     ▼
Storage Backend
```

This abstraction separates application requirements from storage implementation, improving portability across environments.

***

## Namespace Considerations

### Persistent Volumes (PV)

✅ Cluster-wide resource  
✅ Not namespaced

### Persistent Volume Claims (PVC)

✅ Namespaced resource

### Important Rule

A Pod can only use PVCs that exist within the same namespace.

```text
Namespace A
 ├─ Pod A
 └─ PVC A

Namespace B
 ├─ Pod B
 └─ PVC B
```

Pod A cannot directly mount PVC B.

***

## ConfigMaps and Secrets as Volumes

ConfigMaps and Secrets can be mounted into Pods as volumes without using PVs or PVCs.

### ConfigMap

Used for:

* Application configuration
* Environment settings
* Configuration files

### Secret

Used for:

* Passwords
* API keys
* Certificates
* Authentication tokens

### Characteristics

* Managed by Kubernetes
* Namespaced resources
* Not intended for persistent application data
* Do not use PV/PVC architecture

***

## Key Takeaways

* Kubernetes does not provide persistent storage automatically.
* Volumes decouple data from the Pod lifecycle.
* Local volumes are suitable for temporary or configuration data.
* Remote volumes are preferred for production and database workloads.
* PVs represent actual storage resources.
* PVCs allow applications to request storage without knowing implementation details.
* StorageClasses enable dynamic PV provisioning.
* PVs are cluster-scoped; PVCs are namespace-scoped.
* Pods can only access PVCs within their own namespace.
* ConfigMaps and Secrets can be mounted as volumes but are not persistence mechanisms.

***


</details>

## Projects
1. [Deploying a MongoDB and Mongo Express into local K8s cluster](https://github.com/bigtony79/k8s-mongodb-mongoexpress) 
