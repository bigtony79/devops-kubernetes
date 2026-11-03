# Deploy a web application in K8s cluster from private Docker registry

## Overview

## Technologies used
* Kubernetes
* Helm
* AWS
* ECR
* Docker

## Project Description:
* Create Secret for credentials for the private Docker registry
* Configure the Docker registry secret in application Deployment component
* Deploy web application image from our private Docker registry in K8s cluster

## Pre-requisites

## Steps

### 1. Create a Linode Kubernetes Engine (LKE) Cluster
 
Create a new Kubernetes cluster using Linode Kubernetes Engine (LKE).
 
During cluster creation:
 
- Select the desired Kubernetes version.
- Choose a data center region.
- Configure the worker node pool size and instance type.
- Review the configuration and create the cluster.


In this project, the Kubernetes control plane is fully managed by Linode, allowing us to focus on deploying and managing applications.
 
![LKE_deployment](images/linode_k8s_cluster_1.png)
![LKE_deployment](images/linode_k8s_cluster_2.png)
![LKE_deployment](images/linode_k8s_cluster_3.png)
![LKE_deployment](images/linode_k8s_cluster_4.png)
![LKE_deployment](images/linode_k8s_cluster_5.png)


### 2. Wait for the Cluster to Become Available
 
After provisioning begins, wait for the cluster status to change to **Running**.


The Kubernetes worker nodes must be fully initialized before applications can be deployed.


Verify the cluster status in the Linode Cloud Manager.


![LKE_deployment](images/linode_k8s_cluster_6.png)


### 3. Download the Kubeconfig File
 
Once the cluster is ready, download the cluster's **kubeconfig** file from the LKE portal.
 
The kubeconfig file contains:
 
- Cluster endpoint information
- Authentication credentials
- Context configuration used by kubectl


Save the file locally.


![LKE_deployment](images/linode_k8s_cluster_7.png)


### 4. Configure kubectl to Connect to the LKE Cluster
 
Before configuring the kubeconfig file, verify whether kubectl is currently connected to a cluster.
 
```bash
kubectl get nodes
```
 
If no cluster is configured, an error similar to the following may be displayed:
 
```text
The connection to the server localhost:8080 was refused
```
 
![set_kubectl](images/set_kubectl_linode_1.png)


Configure kubectl to use the downloaded kubeconfig file:
 
```bash
export KUBECONFIG=test-kubeconfig.yaml
```
 
Verify connectivity:
 
```bash
kubectl get nodes
```


![set_kubectl](images/set_kubectl_linode_2.png)
### 1. Create a Linode Kubernetes Engine (LKE) Cluster
 
Create a new Kubernetes cluster using Linode Kubernetes Engine (LKE).
 
During cluster creation:
 
- Select the desired Kubernetes version.
- Choose a data center region.
- Configure the worker node pool size and instance type.
- Review the configuration and create the cluster.


In this project, the Kubernetes control plane is fully managed by Linode, allowing us to focus on deploying and managing applications.
 
![LKE_deployment](images/linode_k8s_cluster_1.png)
![LKE_deployment](images/linode_k8s_cluster_2.png)
![LKE_deployment](images/linode_k8s_cluster_3.png)
![LKE_deployment](images/linode_k8s_cluster_4.png)
![LKE_deployment](images/linode_k8s_cluster_5.png)


### 2. Wait for the Cluster to Become Available
 
After provisioning begins, wait for the cluster status to change to **Running**.


The Kubernetes worker nodes must be fully initialized before applications can be deployed.


Verify the cluster status in the Linode Cloud Manager.


![LKE_deployment](images/linode_k8s_cluster_6.png)


### 3. Download the Kubeconfig File
 
Once the cluster is ready, download the cluster's **kubeconfig** file from the LKE portal.
 
The kubeconfig file contains:
 
- Cluster endpoint information
- Authentication credentials
- Context configuration used by kubectl


Save the file locally.


![LKE_deployment](images/linode_k8s_cluster_7.png)


### 4. Configure kubectl to Connect to the LKE Cluster
 
Before configuring the kubeconfig file, verify whether kubectl is currently connected to a cluster.
 
```bash
kubectl get nodes
```
 
If no cluster is configured, an error similar to the following may be displayed:
 
```text
The connection to the server localhost:8080 was refused
```
 
![set_kubectl](images/set_kubectl_linode_1.png)


Configure kubectl to use the downloaded kubeconfig file:
 
```bash
export KUBECONFIG=test-kubeconfig.yaml
```
 
Verify connectivity:
 
```bash
kubectl get nodes
```


![set_kubectl](images/set_kubectl_linode_2.png)
### 1. Create a Linode Kubernetes Engine (LKE) Cluster
 
Create a new Kubernetes cluster using Linode Kubernetes Engine (LKE).
 
During cluster creation:
 
- Select the desired Kubernetes version.
- Choose a data center region.
- Configure the worker node pool size and instance type.
- Review the configuration and create the cluster.


In this project, the Kubernetes control plane is fully managed by Linode, allowing us to focus on deploying and managing applications.
 
![LKE_deployment](images/linode_k8s_cluster_1.png)
![LKE_deployment](images/linode_k8s_cluster_2.png)
![LKE_deployment](images/linode_k8s_cluster_3.png)
![LKE_deployment](images/linode_k8s_cluster_4.png)
![LKE_deployment](images/linode_k8s_cluster_5.png)


### 2. Wait for the Cluster to Become Available
 
After provisioning begins, wait for the cluster status to change to **Running**.


The Kubernetes worker nodes must be fully initialized before applications can be deployed.


Verify the cluster status in the Linode Cloud Manager.


![LKE_deployment](images/linode_k8s_cluster_6.png)


### 3. Download the Kubeconfig File
 
Once the cluster is ready, download the cluster's **kubeconfig** file from the LKE portal.
 
The kubeconfig file contains:
 
- Cluster endpoint information
- Authentication credentials
- Context configuration used by kubectl


Save the file locally.


![LKE_deployment](images/linode_k8s_cluster_7.png)


### 4. Configure kubectl to Connect to the LKE Cluster
 
Before configuring the kubeconfig file, verify whether kubectl is currently connected to a cluster.
 
```bash
kubectl get nodes
```
 
If no cluster is configured, an error similar to the following may be displayed:
 
```text
The connection to the server localhost:8080 was refused
```
 
![set_kubectl](images/set_kubectl_linode_1.png)


Configure kubectl to use the downloaded kubeconfig file:
 
```bash
export KUBECONFIG=test-kubeconfig.yaml
```
 
Verify connectivity:
 
```bash
kubectl get nodes
```


![set_kubectl](images/set_kubectl_linode_2.png)

### 5. Go to ECR and obtain docker login command

### 6. Run command to create ~/.docker/config.json file

### 7. Create secret off the config.js file

### 8. Deploy container; image name must contain full URL; add secret reference in the deployment

## Learning Outcomes

