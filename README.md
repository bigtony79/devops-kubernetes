# Module 10 : Container Orchestration with Kubernetes
This repository documents my hands‑on journey learning Kubernetes. It contains notes, references, and links to practical projects as I build deeper understanding of container orchestration, cluster operations, and cloud‑native best practices.
## Notes
<details>
  <summary>Introduction to Kubernetes</summary>
    
### What is Kubernetes?

* Kubernetes (often abbreviated as K8s) is an open‑source container orchestration platform.
* Originally developed by Google, based on their internal container management experience.
* It helps deploy, manage, and scale containerized applications across different environments such as on‑premises, cloud, and hybrid setups.

### Why Kubernetes?

* Modern applications have shifted from monolithic architectures to microservices.
* The rise of containers (e.g., Docker) increased the need for a system to manage large numbers of containers.
* Kubernetes automates container operations such as deployment, scaling, networking, and recovery, making it easier to run applications reliably at scale.

### Key Features of Kubernetes

##### High Availability

* Ensures applications continue running with minimal or no downtime.
* Automatically replaces or reschedules failed containers.


##### Scalability

* Supports manual and automatic scaling of applications based on demand.


##### Disaster Recovery

* Enables self‑healing by restarting failed pods.
* Supports rolling updates, rollbacks, and data persistence mechanisms.

</details>

<details>
    <summary>Main Kubernetes Components</summary>

### Worker Node

* A worker node is a virtual or physical machine that runs containerized applications.
* Each worker node hosts:
    * Pods
    * A container runtime (e.g., containerd)
    * kubelet and networking components

### Pod

* A Pod is the smallest deployable unit in Kubernetes.
* Acts as a wrapper around one or more containers (most commonly one).
* Key characteristics:
    - Usually one application per Pod
    - All containers in a Pod:
        * Share the same network namespace
        * Communicate via localhost
    - Each Pod gets its own IP address
* Pods are ephemeral:
    - If a Pod fails, Kubernetes creates a new one
    - The new Pod gets a new IP address

### Service

* A Service provides a stable network endpoint to access Pods.
* Abstracts over a set of Pods, not individual Pods.
* Key features:
    - Uses labels and selectors to find Pods
    - Provides a stable IP address and DNS name
    - Performs automatic load balancing
* Common Service types:
    - ClusterIP – Internal access only (default)
    - NodePort – Exposes a service on each node
    - LoadBalancer – Exposes a service externally
* Ensures traffic is redirected to healthy Pods if one fails.

### Ingress

* Ingress manages external HTTP/HTTPS traffic into the cluster.
* Enables:
    * Host‑based routing
    * Path‑based routing
    * TLS termination
* Requires an Ingress Controller (e.g., NGINX, Traefik, AWS ALB).


### ConfigMap

* Stores non‑confidential configuration data, such as:
    * Database URLs
    * Feature flags
    * Application settings
* Allows configuration to be externalized from application code.
* Can be consumed by Pods as:
    * Environment variables
    * Mounted configuration files


⚠️ **Do not store sensitive data in ConfigMaps.**

### Secret


Designed to store sensitive information, including:

Passwords
Tokens
API keys


Data is stored in Base64‑encoded format
⚠️ Base64 encoding is not encryption.
Secrets can be mounted or referenced by:

Pods
Deployments


Often integrated with external secrets managers for enhanced security.


Volume

Volumes provide persistent storage for Pods.
Supported storage backends include:

Local disks
Network storage
Cloud storage solutions


Key points:

Pods are ephemeral; Volumes persist beyond Pod restarts
Persistent storage is managed using:

PersistentVolumes (PV)
PersistentVolumeClaims (PVC)




Kubernetes orchestrates storage, but data durability depends on the backend.


What Happens If a Pod Fails?

Kubernetes automatically:

Recreates the Pod
Schedules it on the same or a different node


The associated Service:

Updates routing
Redirects traffic to healthy Pods without client impact




Deployment (Stateless Applications)

A Deployment defines the desired state for application Pods.
Key capabilities:

Manages replicas
Handles rolling updates and rollbacks
Automatically replaces failed Pods


Best suited for stateless applications.
In practice, you usually work with Deployments rather than raw Pods.


StatefulSet

Designed for stateful applications (e.g., databases).
Key features:

Stable Pod names and identities
Stable, persistent storage per Pod
Ordered startup, shutdown, and scaling


More complex to manage than Deployments.
Databases are often run outside Kubernetes to reduce operational complexity.


DaemonSet

Ensures exactly one Pod runs on each node.
No need to define a replica count.
Automatically:

Deploys Pods when new nodes are added
Removes Pods when nodes are removed


Common use cases:

Log collectors
Monitoring and observability agents
Node‑level system services
Worker Node

A worker node is a virtual or physical machine that runs containerized applications.
Each worker node hosts:

Pods
A container runtime (e.g., containerd)
kubelet and networking components




Pod

A Pod is the smallest deployable unit in Kubernetes.
Acts as a wrapper around one or more containers (most commonly one).
Key characteristics:

Usually one application per Pod
All containers in a Pod:

Share the same network namespace
Communicate via localhost


Each Pod gets its own IP address


Pods are ephemeral:

If a Pod fails, Kubernetes creates a new one
The new Pod gets a new IP address




Service

A Service provides a stable network endpoint to access Pods.
Abstracts over a set of Pods, not individual Pods.
Key features:

Uses labels and selectors to find Pods
Provides a stable IP address and DNS name
Performs automatic load balancing


Common Service types:

ClusterIP – Internal access only (default)
NodePort – Exposes a service on each node
LoadBalancer – Exposes a service externally


Ensures traffic is redirected to healthy Pods if one fails.


Ingress

Ingress manages external HTTP/HTTPS traffic into the cluster.
Enables:

Host‑based routing
Path‑based routing
TLS termination


Requires an Ingress Controller (e.g., NGINX, Traefik, AWS ALB).


ConfigMap

Stores non‑confidential configuration data, such as:

Database URLs
Feature flags
Application settings


Allows configuration to be externalized from application code.
Can be consumed by Pods as:

Environment variables
Mounted configuration files



⚠️ Do not store sensitive data in ConfigMaps.

Secret

Designed to store sensitive information, including:

Passwords
Tokens
API keys


Data is stored in Base64‑encoded format
⚠️ Base64 encoding is not encryption.
Secrets can be mounted or referenced by:

Pods
Deployments


Often integrated with external secrets managers for enhanced security.


Volume

Volumes provide persistent storage for Pods.
Supported storage backends include:

Local disks
Network storage
Cloud storage solutions


Key points:

Pods are ephemeral; Volumes persist beyond Pod restarts
Persistent storage is managed using:

PersistentVolumes (PV)
PersistentVolumeClaims (PVC)




Kubernetes orchestrates storage, but data durability depends on the backend.


What Happens If a Pod Fails?

Kubernetes automatically:

Recreates the Pod
Schedules it on the same or a different node


The associated Service:

Updates routing
Redirects traffic to healthy Pods without client impact




Deployment (Stateless Applications)

A Deployment defines the desired state for application Pods.
Key capabilities:

Manages replicas
Handles rolling updates and rollbacks
Automatically replaces failed Pods


Best suited for stateless applications.
In practice, you usually work with Deployments rather than raw Pods.


StatefulSet

Designed for stateful applications (e.g., databases).
Key features:

Stable Pod names and identities
Stable, persistent storage per Pod
Ordered startup, shutdown, and scaling


More complex to manage than Deployments.
Databases are often run outside Kubernetes to reduce operational complexity.


DaemonSet

Ensures exactly one Pod runs on each node.
No need to define a replica count.
Automatically:

Deploys Pods when new nodes are added
Removes Pods when nodes are removed


Common use cases:

Log collectors
Monitoring and observability agents
Node‑level system services