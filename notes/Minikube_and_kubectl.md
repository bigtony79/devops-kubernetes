# Minikube and kubectl

This section covers Minikube and kubectl, two essential tools for running and interacting with Kubernetes on a local machine.

## Minikube

* Minikube is a single‑node Kubernetes cluster designed for local development and learning.
* Both the control plane and worker node components run on the same node.
* Commonly used for:
    * Learning Kubernetes concepts
    * Testing manifests locally
    * Experimenting without a full multi‑node cluster

### Key Features

* Comes with Docker pre‑installed as the container runtime.
* Supports multiple drivers:
    * Docker
    * VirtualBox
    * Hyper‑V
* Can run:
    * As a virtual machine
    * As a Docker container on your local machine


### Starting Minikube

```
minikube start --driver=docker
```


### Checking Minikube Status

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

### Key Points

* Works with:
    * Local clusters (Minikube)
    * Managed Kubernetes services (EKS, GKE, AKS)
* Automatically installed and configured when Minikube is installed.
* Uses a kubeconfig file to determine which cluster to communicate with.

## Summary

* Minikube provides an easy way to run a local, single‑node Kubernetes cluster.
* kubectl is the primary tool for interacting with any Kubernetes cluster.
* Together, they form the foundation for local Kubernetes development and learning.
