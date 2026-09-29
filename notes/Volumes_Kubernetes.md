# Volumes - Persisting Application Data

## Why Volumes Matter

Containers are ephemeral by design. When a Pod is deleted or recreated, any data stored inside the container is lost. Kubernetes 
therefore requires external storage configuration for applications that need persistent data.

## Persistent Storage Requirements

* Data must not depend on the Pod lifecycle.
* Storage should remain available after Pod restarts or rescheduling.
* Storage should be accessible across cluster nodes.
* Data should survive node failures and cluster disruptions.
* Critical for stateful workloads such as databases.

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
