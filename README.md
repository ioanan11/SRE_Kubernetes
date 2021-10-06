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

## nginx-service.yml
```
---

# Select the type of API version and type of service/object
apiVersion: v1
kind: Service

# Metadata for name
metadata:
  name: nginx-deployment
  namespace: default

# Specification to include ports
spec:
  ports:
  - nodePort: 30442
    port: 80
    protocol: TCP
    targetPort: 80

# Let's define the selector and label to connect to nginx
  selector:
    app: nginx

  # Creating LoadBalancer type of deployment
  type: LoadBalancer

```

# How to deploy nodejs with mongoDB

## Step 1: nodejs
 
### node-deploy.yml

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: node
spec:
  selector:
    matchLabels:
      app: node
  replicas: 3
  template:
    metadata:
      labels:
        app: node
    spec:
      containers:
        - name: node
          image: zeeshanj/sre_node_app:latest

          ports:
            - containerPort: 3000

          #env:
          #- name: DB_HOST
            #value: mongodb://mongo:27017/posts
```
Run `kubectl create -f node-deploy.yml`

Notice the last 3 lines are commented out. We will uncomment them after we have our mongoDB pods running.

### node_svc.yml

```
---
apiVersion: v1
kind: Service
metadata:
  name: node
spec:
  selector:
    app: node
  ports:
    - port: 3000
      targetPort: 3000
  type: LoadBalancer

```
Run `kubectl create -f node_svc.yml`

## Step 2: MongoDB

### mongo_deploy.yml
```
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mongo
spec:
  selector:
    matchLabels:
      app: mongo
  replicas: 1
  template:
    metadata:
      labels:
        app: mongo
    spec:
      containers:
        - name: mongo
          image: mongo:latest
          ports:
            - containerPort: 27017
          #volumeMounts:
            #- name: storage
              #mountPath: /data/db
      #volumes:
        #- name: storage
          #persistentVolumeClaim:
            #claimName: mongo-db-claim

```
Run `kubectl create -f mongo_deploy.yml`

### mongo_service.yml

```
---
apiVersion: v1
kind: Service
metadata:
  name: mongo
spec:
  selector:
    app: mongo
  ports:
    - port: 27017
      targetPort: 27017
```
Run `kubectl create -f mongo_service.yml`

## Step 3: Uncomment the lines
Uncomment the last three lines in node-deploy. Delete both the service and deploy for node and create them again.

```
kubectl delete service node
kubectl delete deploy node
kubectl create -f node-deploy.yml
kubectl create -f node_svc.yml
```
## Step 4: Seed the posts

`localhost:3000/posts` page looks like this now:

![alt text]()

It needs to be seeded. The command for that is `kubectl exec pod_name env node seeds/seed.js`

You can get the pod name using `kubectl get pods`. The page should look like this:

![alt text]()
