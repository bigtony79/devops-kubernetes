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

### 1. Get LKE working
Please follow steps 1 - 4 from projects/Helm_Stateful_service_MongoDB/README.md

### 2. Go to ECR and obtain docker login command

### 3. Run command to create ~/.docker/config.json file

### 4. Create secret off the config.js file

### 5. Deploy container; image name must contain full URL; add secret reference in the deployment

## Learning Outcomes

