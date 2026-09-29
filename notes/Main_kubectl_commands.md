# Main kubectl commmands

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


