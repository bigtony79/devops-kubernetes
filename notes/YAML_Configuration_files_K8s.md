# YAML Configuration Files in Kubernetes

Kubernetes uses YAML configuration files to define and manage cluster resources such as Deployments, Pods, and Services. These files 
describe the desired state of an application, which Kubernetes continuously works to maintain.


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


