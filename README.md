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

* Designed to store sensitive information, including:
    * Passwords
    * Tokens
    * API keys
* Data is stored in **Base64‑encoded format**
    * ⚠️ Base64 encoding is not encryption.
* Secrets can be mounted or referenced by:
    * Pods
    * Deployments
* Often integrated with external secrets managers for enhanced security.

### Volume

* Volumes provide persistent storage for Pods.
* Supported storage backends include:
    * Local disks
    * Network storage
    * Cloud storage solutions
* Key points:
    * Pods are ephemeral; Volumes persist beyond Pod restarts
    * Persistent storage is managed using:
        * PersistentVolumes (PV)
        * PersistentVolumeClaims (PVC)
* Kubernetes orchestrates storage, but data durability depends on the backend.


### What Happens If a Pod Fails?

* Kubernetes automatically:
    * Recreates the Pod
    * Schedules it on the same or a different node
* The associated Service:
    * Updates routing
    * Redirects traffic to healthy Pods without client impact

### Deployment (Stateless Applications)

* A Deployment defines the desired state for application Pods.
* Key capabilities:
    * Manages replicas
    * Handles rolling updates and rollbacks
    * Automatically replaces failed Pods
* Best suited for stateless applications.
* In practice, you usually work with Deployments rather than raw Pods.

### StatefulSet

* Designed for stateful applications (e.g., databases).
* Key features:
    * Stable Pod names and identities
    * Stable, persistent storage per Pod
    * Ordered startup, shutdown, and scaling
* More complex to manage than Deployments.
* Databases are often run outside Kubernetes to reduce operational complexity.


### DaemonSet

* Ensures exactly one Pod runs on each node.
* No need to define a replica count.
* Automatically:
    * Deploys Pods when new nodes are added
    * Removes Pods when nodes are removed
* Common use cases:
    * Log collectors
    * Monitoring and observability agents
    * Node‑level system services
    * Worker Node

</details>

<details>
    <summary>Kubernetes Architecture</summary>
Kubernetes follows a control plane / worker node architecture. The control plane manages the cluster, while worker nodes run the application workloads.

### Control Plane
The Control Plane is responsible for managing the overall state of the cluster and making global decisions.

#### API Server
* The kube‑apiserver is the entry point to the Kubernetes cluster.
* All clients (kubectl, UI dashboards, CI/CD pipelines) interact with the cluster through the API server.
* Responsibilities include:
    * Athentication and authorization
    * Request validation
    * Acting as the cluster’s gateway and gatekeeper

#### Scheduler
* The kube‑scheduler decides where Pods should run.
* It evaluates available nodes based on:
    * Resource requirements (CPU, memory)
    * Node availability
    * Constraints and affinities
* Assigns Pods to the most appropriate worker node.


#### Controller Manager

* The kube‑controller‑manager runs a set of controllers that:
    * Monitor the desired vs. actual state of the cluster
    * Detect cluster state changes
    * Take action to reconcile differences (self‑healing)
* Examples:
    * Node controller
    * ReplicaSet controller
    * Deployment controller
 
#### etcd

* etcd is a distributed key‑value store used by Kubernetes.
* Often referred to as the “brain” of the cluster.
* Stores:
    * Cluster state
    * Configuration data
    * Metadata
* ⚠️ **Application data is NOT stored in etcd**
* Critical for cluster reliability and consistency.

### Worker Nodes
Worker nodes are responsible for running application workloads.
* Each node can run multiple Pods
* Nodes communicate with the control plane to receive instructions and report status

#### Core Components on Every Node
##### Container Runtime
* Responsible for running containers.
* Examples: Docker, containerd, CRI‑O

##### kubelet
* The kubelet is the agent that runs on each node.
* Responsibilities:
    * Communicates with the API server
    * Interacts with the container runtime
    * Starts and manages Pods and containers
    * Reports node and Pod status back to the control plane

##### kube‑proxy

* kube‑proxy is a network component.
* Provides:
    * Intelligent request forwarding
    * Service‑to‑Pod networking
* Maintains network rules to:
    * Route traffic to the correct Pods
    * Enable load balancing via Services

</details>

<details>
    <summary>Minikube and kubectl</summary>
This section covers Minikube and kubectl, two essential tools for running and interacting with Kubernetes on a local machine.

## Minikube

* Minikube is a single‑node Kubernetes cluster designed for local development and learning.
* Both the control plane and worker node components run on the same node.
* Commonly used for:
    * Learning Kubernetes concepts
    * Testing manifests locally
    * Experimenting without a full multi‑node cluster


## Key Features

* Comes with Docker pre‑installed as the container runtime.
* Supports multiple drivers:
    * Docker
    * VirtualBox
    * Hyper‑V
* Can run:
    * As a virtual machine
    * As a Docker container on your local machine

## Starting Minikube
```
minikube start --driver=docker
```

## Checking Minikube Status
``` 
 minikube status
```

## kubectl

* kubectl is the command‑line interface (CLI) for Kubernetes.
* Used to:
    * Deploy applications
    * Inspect cluster resources
    * Manage and debug workloads
* kubectl communicates directly with the Kubernetes API server in the control plane.

## Key Points

* Works with:
    * Local clusters (Minikube)
    * Managed Kubernetes services (EKS, GKE, AKS)
* Automatically installed and configured when Minikube is installed.
* Uses a kubeconfig file to determine which cluster to communicate with.

## Summary

* Minikube provides an easy way to run a local, single‑node Kubernetes cluster.
* kubectl is the primary tool for interacting with any Kubernetes cluster.
* Together, they form the foundation for local Kubernetes development and learning.

</details>

<details>
<summary>Main kubectl commmands</summary>
This section covers commonly used kubectl commands for managing and troubleshooting Kubernetes clusters.

## Cluster and Resource Status
### Get Node Status
```
kubectl get nodes
```
Displays the status of all worker nodes in the cluster.

### List Pods
```
kubectl get pods
```
Lists Pods in the current namespace.

### List Services
```
kubectl get services
```
Shows all Services and their exposed IPs or ports.

## Creating Resources
### Create Kubernetes Components
```
kubectl create <resource>
```
Commonly used to create resources like:
* Deployments
* Services
* ConfigMaps


### Create a Deployment (Pods are created indirectly)

Pods are not created directly using kubectl create. Instead, Pods are managed by higher‑level abstractions such as Deployments.

Example:
```
kubectl create deployment nginx-depl --image=nginx
```
This command:
* Creates a Deployment
* Automatically creates:
    * A ReplicaSet
    * One or more Pods

## Viewing Deployment and ReplicaSet Status

### Get Deployments
```
kubectl get deployments
```
Displays the status of all Deployments.

### Get ReplicaSets
```
kubectl get replicaset
```
A ReplicaSet ensures the specified number of Pod replicas are running.

## Kubernetes Object Hierarchy
```
Deployment
 └── ReplicaSet
      └── Pod
           └── Container
```
* Deployment: Manages updates and scaling
* ReplicaSet: Manages Pod replicas
* Pod: Abstraction over containers
* Container: Runs the application


## Editing Live Resources
Edit a Deployment
```
kubectl edit deployment <deployment-name>
```
* Opens the resource definition in a text editor
* Changes are applied immediately
* Useful for quick updates (not ideal for long‑term config management)


## Debugging Pods
### View Pod Logs
```
kubectl logs <pod-name>
```
Displays logs from the container in the Pod.

### Describe a Pod
```
kubectl describe pod <pod-name>
```
Provides detailed information such as:
* Events
* Environment variables
* Resource usage
* Scheduling details


### Access a Pod’s Container Shell
```
kubectl exec -it <pod-name> -- /bin/bash
```
* Opens an interactive shell inside the container
* Useful for debugging and inspection
* Requires the container image to have a shell available


## Deleting Resources and Applying Configuration Files

### Delete a Deployment
```
kubectl delete deployment <deployment-name>
```
Deletes:
* The Deployment
* Associated ReplicaSets
* All managed Pods

### Apply a Configuration File
```
kubectl apply -f <configuration-file>.yaml
```
* Creates or updates resources defined in a YAML file
* Kubernetes reconciles the desired state with the actual state
* Preferred approach for declarative infrastructure management

## Summary

* kubectl is the primary interface for managing Kubernetes clusters
* Most workloads are managed using Deployments, not Pods directly
* Debugging involves logs, describe, and exec commands
* Declarative configs (kubectl apply) are best practice for production
</details>

<details>
<summary>YAML Configuration Files in Kubernetes</summary>
Kubernetes uses YAML configuration files to define and manage cluster resources such as Deployments, Pods, and Services. These files describe the desired state of an application, which Kubernetes continuously works to maintain.

## Structure of a Kubernetes Configuration File
A typical Kubernetes configuration file consists of three main sections:

### Metadata
* Identifies the Kubernetes resource.
* Common fields include:
    * name – Unique name of the resource
    * labels – Key‑value pairs used for identification and grouping
```
    metadata:  
        name: nginx-deployment
        labels:
            app: nginx
```

### Spec
* Defines the desired state of the resource.
* Varies depending on the resource type (Deployment, Service, etc.).
* Includes configuration such as:
    * Number of replicas
    * Container images
    * Ports
    * Resource limits
```
    spec:  
        replicas: 2
```

### Status

* Automatically generated and managed by Kubernetes
* Reflects the current state of the resource.
* Kubernetes retrieves this information from etcd, which stores cluster state.
* ❌ Never defined manually in YAML files.


## YAML as the Configuration Format

* Kubernetes configuration files are typically written in YAML.
* Declarative: you define what you want, not how to achieve it.
* YAML files are:
    * Stored alongside application code
    * Version‑controlled in Git
    * Applied using kubectl apply


## Template Section (Pod Blueprint)
For higher‑level objects such as Deployments, the configuration includes a template section.
* The template:
    * Defines how Pods should be created
    * Has its own metadata and spec
    * Acts as a blueprint for Pods
```
    template:  
        metadata:    
            labels:      
                app: nginx  
    spec:
        containers:
            - name: nginx
              image: nginx:latest
```

## Connecting Components: Labels, Selectors, and Ports
Kubernetes components communicate and connect using labels and selectors.

### Labels
* Assigned to Pods via the template section
* Used for grouping and selection
```
    labels:  
        app: nginx
```

### Selectors

* Used by Deployments and Services to identify which Pods they manage or expose
* Selectors must match Pod labels exactly
```
    selector:  
        matchLabels:    
            app: nginx
```

### Ports

* Define how containers expose traffic
* Used by Services to route network requests
```
    ports:  
        - containerPort: 80
```

## Sample Deployment Configuration YAML
Below is a complete, minimal Deployment YAML file example:
```
apiVersion: apps/v1
kind: Deployment
metadata:  
    name: nginx-deployment  
    labels:    
        app: nginx
spec:  
    replicas: 2  
    selector:
        matchLabels:      
            app: nginx  
    template:   
         metadata:
           labels:
            app: nginx    
    spec:      
        containers:
           - name: nginx
             image: nginx:latest
             ports:
              - containerPort: 80
```

### What This File Does

* Creates a Deployment named nginx-deployment
* Ensures 2 replicas of the Pod are always running
* Uses labels and selectors to manage Pods
* Runs an NGINX container exposing port 80


## Useful kubectl Commands for YAML

### View Extended Pod Information
```
kubectl get pods -o wide
```
Displays additional Pod details such as node placement and IP addresses.

### Export a Deployment to YAML
```
kubectl get deployment nginx-deployment -o yaml > nginx-deployment.yaml
```
* Outputs the full Deployment definition (including status)
* Useful for:
    * Learning
    * Debugging
    * Creating reusable templates
* **⚠️ Tip: Remove the status section before re‑applying exported files.**

## Key Takeaways

* Kubernetes YAML files define the desired state of resources
* Only metadata and spec are user‑defined
* status is managed internally by Kubernetes
* Labels and selectors are critical for component communication
* YAML files enable declarative, version‑controlled infrastructure
</details>

<details>
<summary>Namespaces - Organizing components</summary>
</details>

## Projects
1. [Deploying a MongoDB and Mongo Express into local K8s cluster] 