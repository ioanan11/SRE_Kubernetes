# Kubernetes

## What is Kubernetes?
Kubernetes (also known as k8s or “kube”) is an open source container orchestration platform that automates many of the manual processes involved in deploying, managing, and scaling containerized applications.

For a comprehensive documentation, please visit the official kubernetes website: https://kubernetes.io/

## What are the benefits of Kubernetes?
Kubernetes is the fastest growing project in the history of open Source software, after Linux.

- open source
- market leader
- self-healing: Kubernetes's self-healing property ensures that the clusters always function at the optimal state

## Kubernetes Architecture

![alt text]()

## Setting up Kubernetes 

## Commands

Everything starts with `kubectl`

![alt text]()

`kubectl get service` - create a cluster (we can see the IP, port etc)
 
# Deployment

## nginx-deploy.yml

```
# K8 works with API version to declare the resources
# We have to declare the apiVersion and the kind of service components

apiVersion: apps/v1
kind: Deployment
metadata:
   name: nginx-deployment # naming the deployment
spec:
   selector:
      matchLabels:
         app: nginx # look for this server to match with K8 service
   # Let's create a replica set of this with 3 instances/pods
   replicas: 2

   # template to use its label for K8 service to launch in browser
   template:
      metadata:
         labels:
            app: nginx # Label to connect service

      spec:
            containers:
            - name: nginx
              image: ioanan11/sre_cusomised_nginx:latest
              ports:
              - containerPort: 80
```

