# Kubernetes Architecture

Kubernetes follows a control plane / worker node architecture. The control plane manages the cluster, while worker nodes run the 
application workloads.


## Control Plane

The Control Plane is responsible for managing the overall state of the cluster and making global decisions.


## API Server

* The kube‑apiserver is the entry point to the Kubernetes cluster.
* All clients (kubectl, UI dashboards, CI/CD pipelines) interact with the cluster through the API server.
* Responsibilities include:
    * Athentication and authorization
    * Request validation
    * Acting as the cluster’s gateway and gatekeeper


### Scheduler

* The kube‑scheduler decides where Pods should run.
* It evaluates available nodes based on:
    * Resource requirements (CPU, memory)
    * Node availability
    * Constraints and affinities
* Assigns Pods to the most appropriate worker node.

## Controller Manager

* The kube‑controller‑manager runs a set of controllers that:
    * Monitor the desired vs. actual state of the cluster
    * Detect cluster state changes
    * Take action to reconcile differences (self‑healing)
* Examples:
    * Node controller
    * ReplicaSet controller
    * Deployment controller
 
## etcd

* etcd is a distributed key‑value store used by Kubernetes.
* Often referred to as the “brain” of the cluster.
* Stores:
    * Cluster state
    * Configuration data
    * Metadata
* ⚠️ **Application data is NOT stored in etcd**
* Critical for cluster reliability and consistency.


## Worker Nodes

Worker nodes are responsible for running application workloads.
* Each node can run multiple Pods
* Nodes communicate with the control plane to receive instructions and report status


## Core Components on Every Node

### Container Runtime

* Responsible for running containers.
* Examples: Docker, containerd, CRI‑O


### kubelet

* The kubelet is the agent that runs on each node.
* Responsibilities:
    * Communicates with the API server
    * Interacts with the container runtime
    * Starts and manages Pods and containers
    * Reports node and Pod status back to the control plane


### kube‑proxy

* kube‑proxy is a network component.
* Provides:
    * Intelligent request forwarding
    * Service‑to‑Pod networking
* Maintains network rules to:
    * Route traffic to the correct Pods
    * Enable load balancing via Services
