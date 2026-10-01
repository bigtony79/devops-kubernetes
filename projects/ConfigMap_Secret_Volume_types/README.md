# Kubernetes Demo Project: ConfigMap and Secret Volume Types

## Overview

This project demonstrates how to use ConfigMaps and Secrets as volume types in Kubernetes. Using the Eclipse Mosquitto MQTT broker, we will deploy a containerized application and compare its default configuration with a deployment that loads custom configuration files and sensitive data through Kubernetes-managed resources.

ConfigMaps provide a mechanism for storing non-sensitive configuration data outside of container images, while Secrets provide secure storage for sensitive information such as passwords, API keys, and certificates. Mounting these resources as volumes promotes better application portability, maintainability, and security.

## Technologies Used

* Kubernetes (Minikube)
* Docker
* Mosquitto
* ConfigMaps
* Secrets

## Project Objectives

* Deploy a Mosquitto MQTT broker in Kubernetes.
* Examine the default application configuration.
* Create and manage ConfigMaps.
* Create and manage Secrets.
* Mount ConfigMaps and Secrets as volumes.
* Separate configuration and sensitive data from container images.
* Verify mounted files from within a running container.

## Pre-requisites

* Docker Desktop
* Minikube
* kubectl

### Verify installation:

```
    docker --version
    minikube version
    kubectl version --client
```

### Start Minikube

```
    minikube start
```

Verify cluster status:

```
    kubectl get nodes
```


## Steps

### 1. Deploy a mosquitto application without volumes

Deploy the Mosquitto application using its default configuration.

```
    kubectl apply -f mosquitto-without-volumes.yaml
```

INSERT SCREENSHOT

Verify that the pod is running:

```
    kubectl get pod
```

INSERT SCREENSHOT

Examine the pod
``` 
    kubectl exec -it <pod-name> -- /bin/sh
    ls
    cd mosquitto
    ls
    cd config/
    ls
    cat mosquitto.conf
    exit
```
This allows us to inspect the default Mosquitto configuration packaged within the container image.

INSERT SCREENSHOT of default mosquitto.conf file

### 2. Delete the existing mosquitto-without-volumes deployment

Remove the deployment before deploying the new version with mounted volumes.

```
    kubectl delete -f mosquitto-without-volumes.yaml
```
INSERT SCREENSHOT

The remaining steps demonstrate how Kubernetes ConfigMaps and Secrets can be mounted into containers as volumes.

### 3. Create ConfigMap and Secrets resources

Create the ConfigMap and Secret objects.

```
    kubectl apply -f config-file.yaml
    kubectl apply -f secret-file.yaml
```

Verify the resources
```
    kubectl get configmap
    kubectl get secret
```

INSERT SCREENSHOT

### 4. Mount the ConfigMap and Secret as Volumes

The manifest **mosquitto-with-volumes.yaml** mounts both resources into the pod and container. This allows useful abstraction for multiple containers. 

```yaml
    spec:
    containers:
      - name: mosquitto
        image: eclipse-mosquitto:2.0
        ports:
          - containerPort: 1883
        volumeMounts:  # mount the volumes in the container
          - name: mosquitto-config
            mountPath: /mosquitto/config
          - name: mosquitto-secret
            mountPath: /mosquitto/secret
            readOnly: true
    volumes: # mount the volumes in the pod
      - name: mosquitto-config
        configMap:
          name: mosquitto-config-file
      - name: mosquitto-secret
        secret:
          secretName: mosquitto-secret-file
```


### 5. Deploy Mosquitto with ConfigMap and Secret Volumes

Deploy the updated application:

```
    kubectl apply -f mosquitto-with-volumes.yaml
```

Verify the pod:

```
    kubectl get pod   
```

INSERT SCREENSHOT

#### Verify the mounted files

Connect to the running container:

```
    kubectl exec -it <pod_name> -- /bin/sh
```

View the mounted secret file:

```
    ls
    cd mosquitto
    ls
    cd secret
    cat secret.file
```
    
View the mounted configuration file:

```    
    cd ..
    cd config
    cat mosquitto.conf
```

INSERT SCREENSHOTS

## Learning outcomes

By completing this project, you will learn how to:

* Deploy containerized applications to Kubernetes.
* Use Minikube as a local Kubernetes development environment.
* Create and manage Kubernetes ConfigMaps.
* Create and manage Kubernetes Secrets.
* Mount ConfigMaps as volumes inside containers.
* Mount Secrets as read-only volumes.
* Separate application configuration from container images.
* Store sensitive data securely within Kubernetes.
* Inspect running containers using kubectl exec.
* Verify mounted files and directories within containers.
* Follow Kubernetes best practices for configuration management.
* Understand how ConfigMaps and Secrets improve application portability and maintainability.
