# Namespaces - Organizing components

## What is a Namespace?

A **Namespace** is a logical partition within a Kubernetes cluster that helps organize and isolate resources.

Think of a Namespace as a **virtual cluster inside a physical Kubernetes cluster**. Namespaces allow multiple teams, environments, or 
applications to share the same cluster while keeping their resources separate.

## Benefits of Namespaces

* Organize resources logically
* Isolate workloads between teams and environments
* Prevent naming conflicts between resources
* Apply resource quotas and limits
* Manage access using Role-Based Access Control (RBAC)
* Separate environments such as:
  * Development
  * Testing
  * Staging
  * Production

## View Existing Namespaces

```bash
kubectl get namespaces
```

or

```bash
kubectl get ns
```

Example output:

```text
NAME              STATUS   AGE
default           Active   30d
kube-system       Active   30d
kube-public       Active   30d
kube-node-lease   Active   30d
```

## Default Kubernetes Namespaces

### kube-system

Contains Kubernetes system components.

Examples:

* kube-apiserver
* kube-controller-manager
* kube-scheduler
* CoreDNS
* kube-proxy

#### Best Practice

⚠️ Avoid creating or modifying your own application resources in this namespace unless you are administering the cluster.

### kube-public

Contains data that should be publicly readable within the cluster.

#### Characteristics

* Readable by all users (including unauthenticated users in some cluster configurations)
* Commonly contains cluster-wide public information

Example:

```bash
kubectl get configmap -n kube-public
```

### kube-node-lease

Stores **Lease objects** associated with cluster nodes.

#### Purpose

* Tracks node heartbeats
* Improves node health monitoring efficiency
* Helps determine node availability

Each node has its own Lease object in this namespace.

### default

The default namespace used when no namespace is explicitly specified.

Example:

```bash
kubectl create deployment nginx --image=nginx
```

The deployment will be created in the **default** namespace unless another namespace is specified.

## Creating a Namespace

### Using kubectl

```bash
kubectl create namespace my-namespace
```

Verify:

```bash
kubectl get namespaces
```

### Using a YAML Manifest

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: my-namespace
```

Apply it:

```bash
kubectl apply -f namespace.yaml
```

## Why Use Namespaces?

Without namespaces, all resources would be placed in the same logical space, making cluster management difficult as applications grow.

Namespaces help:

### Resource Organization

Group related resources together:

* Deployments
* Pods
* Services
* ConfigMaps
* Secrets

For example:

```text
development
staging
production
```

### Multi-Team Isolation

Different teams can work within the same cluster without interfering with each other.

Example:

```text
frontend-team
backend-team
data-engineering-team
```

Resource names can be reused across namespaces:

```text
frontend namespace:
  app-service

backend namespace:
  app-service
```

### Environment Separation

Namespaces are commonly used to separate environments:

```text
myapp-dev
myapp-test
myapp-prod
```

This enables safer deployments and testing.

### Blue/Green Deployments

Namespaces can be used to isolate deployment versions:

```text
blue
green
```

Traffic can be switched between environments during releases.

### Resource Quotas and Limits

Administrators can enforce:

* CPU limits
* Memory limits
* Storage quotas
* Pod count restrictions

This prevents one team from consuming excessive cluster resources.

### Access Control (RBAC)

Permissions can be configured per namespace.

Examples:

* Developers can access only Dev namespace
* QA can access only Test namespace
* Operations team can access all namespaces

## Namespace Resource Scope

Most Kubernetes resources are **Namespaced**:

* Pods
* Deployments
* Services
* ConfigMaps
* Secrets
* ReplicaSets

Some resources are **Cluster-Scoped** and do not belong to any namespace:

* Nodes
* PersistentVolumes (PV)
* StorageClasses
* Namespaces
* ClusterRoles
* ClusterRoleBindings

These resources exist at the cluster level and cannot be isolated within a namespace.

## ConfigMaps and Namespaces

ConfigMaps are namespace-scoped resources.

A ConfigMap created in one namespace cannot be directly referenced from another namespace.

Example:

```text
frontend/config-app
```

is not automatically accessible from:

```text
backend
```

If multiple namespaces require the same configuration, separate ConfigMaps must typically be created in each namespace.

## Communication Across Namespaces

Although resources are isolated by namespace, services can still communicate across namespaces.

### Service DNS Format

```text
<service-name>.<namespace>.svc.cluster.local
```

Example:

```text
database.backend.svc.cluster.local
```
This allows applications in one namespace to connect to services running in another namespace.

## Change the Active Namespace

Instead of specifying a namespace in every command, you can change the current context's default namespace.

```bash
kubectl config set-context --current --namespace=my-namespace
```

Verify the current context:

```bash
kubectl config view --minify | grep namespace
```

After changing the context, subsequent commands will use that namespace by default.

Example:

```bash
kubectl get pods
```

will automatically query pods in:

```text
my-namespace
```

## Key Takeaways

* Namespaces provide logical isolation within a Kubernetes cluster.
* Kubernetes includes four default namespaces:
    - default
    - kube-system
    - kube-public
    - kube-node-lease
* Namespaces help with:
    - Organization
    - Multi-team collaboration
    - Environment separation
    - Resource quotas
    - RBAC access control

* Most workloads are namespace-scoped, but resources like Nodes and PersistentVolumes are cluster-scoped.
* Services can communicate across namespaces using Kubernetes DNS names.
* ConfigMaps and Secrets are namespace-specific resources.



