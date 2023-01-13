## 194: Updating Existing Objects

*Find an existing Pod and update the image that it is using

```
Update the Config file

- Name: client-pod
- Kind: Pod
- image: multi-worker

kubectl -> Master -> Virtual Machine (Node) {
                                            	Pod Name: client-pod
                                              	multi-client container
                                            } 
```

## 195: Declarative Updates in Action

update client-pod.yaml

```
apiVersion: v1
kind: Pod
metadata:
  name: client-pod
  labels:
    component: web
spec:
  containers:
    - name: client
      image: marshad1/multi-worker 			# update multi-client to multi-worker image
      ports:
        - containerPort: 3000
```

```
$ kubectl apply -f client-pod.yaml
pod/client-pod "configured"

$ kubectl get pods
NAME    READY     STATUS      RESTARTS      AGE

How to get detailed info about an object
# kubectl describe <object type> <object name>
$ kubectl describe pod client-pod
```

## 196: Limitations in Config Updates

```
ports:
  - containerPort: 9999 # update port number from 3000 to 9999
 
$ kubectl apply -f client-pod.yaml
```

## 197: Running Containers with Deployments


* Pods 		-> Runs a single set of containers
* Services 	-> Setups up 'networking' in a Kubernetes Cluster
* Deployment 	-> Runs a set of identical pods (one or more)

* Deployment object has a Pod Template

## 198: Quick Note to Prevent an Error

## 199: Deployment Configuration Files

$ touch client-deployment.yaml 				

```
apiVersion: apps/v1
kind: Deployment 					# Obejct typeRuns a set of identical pods
metadata:
  name: client-deployment
spec:
  replicas: 1 						# Number of pods this deployment is supposed to make
  selector:
    matchlabels:
      component: web
  template: 						# Configuration for every single pod using this deployment
    metadata:
      labels:
        component: web
    spec:
      containers:
        - name: client
          image: marshad1/multi-client
          ports:
            - containerPort: 3000
```

## 200: Walking Through the Deployment Config

## 201: Applying a Deployment

```
$ kubectl get pods

Remove an object
$ kubectl delete -f <config file>
$ kubectl delete -f client-pod.yaml
pod "client-pod" deleted

$ kubectl apply -f client-deployment.yaml
deployment.apps/client-deployment created

$ kubectl get pods

$ kubectl get deployments
NAME    DESIRED   CURRENT   UP-TO-DATE    AVAILABLE   AGE
```

## 202: Why Use Services?

```
$ minikube ip						# VM IP adress
$ kubectl get pods -o wide				# Every pod is assigned its own IP address
```

## 203: Scaling and Changing Deployments

```
ports:
  - containerPort: 9999 # change port from 3000 to 9999

$ kubectl apply -f client.deployment.yaml
deployment.apps/client-deployment configured 		# Notice 'configured' vs 'created'

$ kubectl get deployments
$ kubectl get pods
$ kubectl describe pods

replicas: 5

$ kubectl apply -f client-deployment.yaml
$ kubectl get pods

image: stephengrider/multi-worker

$ kubectl apply -f client-deployment.yaml
$ kubectl get pods
```

## 204: Updating Deployment Images

```
$ kubectl apply -f client-deployment.yaml
$ minikube ip
```

## 205: Rebuilding the Client Image

```
$ cd complex/client

# Crate a new Image
$ docker buid -t muarshad01/multi-client . 		# '.' is build context

# Push to docker hub
$ docker push muarshad01/multi-client
```

## 206: Triggering Deployment Updates

```
https://github.com/kubernetes/kubernetes/issues/33664

$ kubectl apply -f client-deployment.yaml
deployment.apps/client-deployment unchanged 		# Notice 'unchanged', it means new config file is same as previous

$ kubectl get pods
$ kubectl get deployments
```

## 207: Imperatively Updating a Deployment's Image

```
- Update the multi-client image
- Tag the image with a version number, push to docker hub
- Run a 'kubectl' command forcing the deployment to user the new image version

$ cd complex/client

$ docker build -t marshad1/multi-client:v5 .
$ docker push <tag-name>
$ docker push marshad1/multi-client:v5

$ kubectl set image <object_type>/<object_name> <container_name>=<new image to use>
$ kubectl set image deployment/client-deployment client=marshad1/multi-client:v5
deployment.extensions/client-deployment image updated

$ kubect get pods
$ minikube ip
```

## 208: Reminder for Docker Desktop's Kubernetes Users

## 209: Multiple Docker Installations

$ docker ps

## 210: Reconfiguring Docker CLI

```
$ cd simplek8s
$ docker ps
$ eval $(minikube docker-env) 			# Temprarily, reconfigures local copy of docker client to 
                                		# connect to the docker server inside the kubernetes node
$ docker ps
```

## 211: Why Mess with Docker in the Node?

```
-- Use all the same debugging techniques we learned with Docker CLI
-- Manually kill containers to test Kubernetes ability to 'self-heal'
-- Deleted cached images in the node

$ docker logs CONTAINER-ID  
$ docker exec -it CONTAINER-ID sh 			# 'i' STDIN of process; -t: readable format 

$ kubectl get pods
$ kubect logs <pod-name>
$ kubect exec -it <pod-name> sh
$ ls

$ docker system prune -a 
```
