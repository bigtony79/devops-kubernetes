# Managed Kubernetes Services
 
When deploying Kubernetes, you can either manage the cluster yourself or use a managed Kubernetes service provided by a cloud provider.
 
## Option 1: Unmanaged Kubernetes Cluster
 
With an unmanaged Kubernetes deployment, you are responsible for setting up and maintaining the entire cluster.
 
### Typical Process 
1. Provision virtual machines or cloud instances.
2. Install and configure Kubernetes components.
3. Configure networking and cluster communication.
4. Deploy applications to the cluster.
5. Manage upgrades, security, backups, monitoring, and troubleshooting.
 
### Advantages 
- Full control over the Kubernetes environment.
- Greater flexibility in cluster configuration.
- Potentially lower infrastructure costs in some scenarios.
 
### Challenges 
- Significant operational overhead.
- Requires Kubernetes expertise.
- Responsible for maintaining the control plane and worker nodes.
- Must handle upgrades, monitoring, scaling, and disaster recovery.
 
While this approach is possible, it requires considerable effort and is often impractical when rapid deployment and simplicity are priorities.

## Option 2: Managed Kubernetes Service
 
A managed Kubernetes service simplifies cluster deployment and operations by offloading much of the infrastructure management to the cloud provider.
 
### Benefits 
- No need to install Kubernetes from scratch.
- Most cluster components are automatically provisioned.
- Control plane management is handled by the cloud provider.
- Faster deployment with minimal configuration.
- Reduced operational effort and maintenance.
 
### Typical Workflow 
1. Select the number of worker nodes.
2. Choose node size (CPU, memory, storage).
3. Select the deployment region.
4. Create the cluster.
5. Connect using `kubectl` and begin deploying applications.
 

Examples of managed Kubernetes services include:
- Amazon EKS (Elastic Kubernetes Service)
- Azure Kubernetes Service (AKS)
- Google Kubernetes Engine (GKE)
- Linode Kubernetes Engine (LKE)

### Example: Linode Kubernetes Engine (LKE)
 
Creating a Kubernetes cluster with LKE is straightforward: 
1. Select worker node specifications (CPU, RAM, and storage).
2. Choose the data center region.
3. Create the cluster with a few clicks.
4. Download the kubeconfig file.
5. Connect to the cluster using **kubectl**
 
Because the Kubernetes control plane is managed by Linode, administrators can focus on running applications rather than maintaining cluster infrastructure.
 
## Data Persistence 
### Unmanaged Kubernetes Cluster
 
When deploying a stateful application, such as MongoDB, you must configure storage yourself.
 
Typical steps include: 
1. Provision physical or cloud storage.
2. Create Persistent Volumes (PVs).
3. Create Persistent Volume Claims (PVCs).
4. Attach storage to database Pods.
5. Manage backups and storage lifecycle.

This provides maximum flexibility but requires additional administration.

### Managed Kubernetes Cluster

Managed Kubernetes services simplify storage management through Storage Classes.

For example, LKE provides a default Storage Class that automatically provisions storage when a Persistent Volume Claim is created.

Benefits include:
* Automatic volume provisioning.
* No manual volume creation.
* Simplified storage management.
* Faster deployment of stateful applications.

```
Database Pod
|
+--> PVC
|
+--> Storage Class
|
+--> Dynamically Created Volume
```

This allows developers to focus on applications rather than storage infrastructure.

## Load Balancing

### Unmanaged Kubernetes Cluster

In a self-managed cluster, you are typically responsible for configuring external access.

A common approach is:

```
Internet
|
v
Ingress Controller
|
v
Services
|
v
Pods
```

Responsibilities include:
* Installing and managing an Ingress Controller.
* Configuring routing rules.
* Managing certificates.
* Scaling and maintaining load balancing infrastructure.

Examples of Ingress Controllers:
* NGINX Ingress Controller
* Traefik
* HAProxy Ingress

### Managed Kubernetes Service

Most cloud providers offer managed load balancers that can be integrated directly with Kubernetes.

Benefits include:
* Automatic load balancer provisioning.
* High availability.
* Simplified routing configuration.
* Native cloud platform integration.

```
Internet
|
v
Cloud Load Balancer
|
v
Worker Nodes
|
v
Pods
```

### Session Stickiness

Cloud load balancers often support session affinity (sticky sessions).

Benefits:
* Client requests consistently reach the same application instance.
* Useful for applications that maintain user session data locally.
* Improves user experience for stateful web applications.

## SSL/TLS Certificates

Securing application traffic is essential in production environments.

A common Kubernetes solution is cert-manager, which automates certificate management.

### Benefits of cert-manager
* Automatic certificate provisioning.
* Automatic certificate renewal.
* Integration with Let's Encrypt and other certificate authorities.
* Reduced administration effort.

Example:

```
User
|
HTTPS
|
Load Balancer / Ingress
|
TLS Certificate Managed by cert-manager
|
Application
```

## Moving Applications Between Cloud Providers

One of Kubernetes' major advantages is workload portability.

However, complete portability becomes more challenging when applications depend heavily on cloud-specific services.

### Vendor Lock-In

Vendor lock-in occurs when an application becomes tightly coupled to services provided by a specific cloud provider.

Examples:
* Proprietary storage services
* Cloud-specific databases
* Cloud-specific networking features
* Provider-specific load balancers

#### Challenges

When migrating to another cloud provider:
* Application configurations may require changes.
* Infrastructure code may need updates.
* Storage and networking components may need redesign.
* Some application code may require modification.

### Best Practices to Reduce Vendor Lock-In
* Use Kubernetes-native resources whenever possible.
* Avoid excessive reliance on cloud-specific features.
* Use standard Kubernetes Storage Classes and Ingress resources.
* Implement Infrastructure as Code (Terraform).
* Design applications to be cloud-agnostic.

## Summary

Managed Kubernetes services significantly reduce the operational burden of running Kubernetes by handling control plane management, storage integration, and load balancing. While unmanaged clusters provide greater control, they require substantial effort to maintain.

For most organizations, managed services such as Amazon EKS, AKS, GKE, and LKE provide the fastest and most efficient path to running production Kubernetes workloads, allowing teams to focus on deploying applications rather than managing cluster infrastructure.