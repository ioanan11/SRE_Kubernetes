# Kubernetes

## What is Kubernetes?
Kubernetes (also known as k8s or “kube”) is an open source container orchestration platform that automates many of the manual processes involved in deploying, managing, and scaling containerized applications.

For a comprehensive documentation, please visit the official kubernetes website: https://kubernetes.io/

![alt text](https://github.com/ioanan11/SRE_Kubernetes/blob/main/Screenshot%202021-10-04%20143843.png)

## What are the benefits of Kubernetes?
Kubernetes is the fastest growing project in the history of open Source software, after Linux.

- **open source**
- **market leader**
- **self-healing**: Kubernetes's self-healing property ensures that the clusters always function at the optimal state

## Kubernetes Architecture

![alt text]()

## Setting up Kubernetes 

In Docker on your localhost, go to **Settings > Kubernetes > Enable Kubernetes > Apply and Restart**.

![alt text]()

The Kubernetes logo should be green and if you hover over it, it should say "Running". Also, to check if it was correctly installed, run `kubectl version`

## Commands

Everything starts with `kubectl`

![alt text](https://github.com/ioanan11/SRE_Kubernetes/blob/main/Screenshot%202021-10-04%20160700.png)

`kubectl get service` - create a cluster (we can see the IP, port etc)
 
# Deployment

We will use 2 YAML files. One for deployment of the app and one to make it available.

Additionally, we will use previous images which can be found on docker hub. It is very important that the images are available, otherwise we will get errors when deploying.

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
              image: ioanan11/sre_nginx_test:v1
              ports:
              - containerPort: 80
```

Now run `kubectl create -f nginx-deploy.yml`
