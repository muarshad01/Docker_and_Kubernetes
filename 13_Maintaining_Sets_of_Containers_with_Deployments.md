## 188: Updating Existing Objects

* Find an existing `Pod` and update the image that it is using

* Update `client-pod.yaml` file

```
Name: client-pod
Kind: Pod
image: multi-worker
```

* `kubectl`
    - `Master`
    - VM (`Node`) {`Kind: Pod`; `name`: client-pod; `image`: <github-id>/multi-client} 

***

## 189: Declarative Updates in Action

* Update `client-pod.yaml`

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
      image: muarshad01/multi-worker 			# Update multi-client to multi-worker image
      ports:
      - containerPort: 3000
```

```
$ kubectl apply -f client-pod.yaml
```
-- pod/client-pod "configured"

```
$ kubectl get pods
```
NAME    READY     STATUS      RESTARTS      AGE

* How to get detailed info about an object?

```
$ kubectl describe <object type> <object name>
$ kubectl describe pod           client-pod
```

***

## 190: Limitations in Config Updates

```
ports:
    - containerPort: 9999                   # Update port number from 3000 to 9999
```

``` 
$ kubectl apply -f client-pod.yaml
```

***

## 191: Running Containers with Deployments

* `Pods`
    - Runs a single set of containers
* `Services`
    - Setups up 'networking' in a `Kubernetes` Cluster
* `Deployment`
    - Runs a set of identical `Pods` (one or more)
    - `Deployment` object has a `Pod Template`

***

## 192: Quick Note to Prevent an Error

***

## 193: Deployment Configuration Files

$ touch `client-deployment.yaml`

```
apiVersion: apps/v1
kind: Deployment 					# Obejct type: Runs a set of identical Pods
metadata:
  name: client-deployment
spec:
  replicas: 1 						# Number of pods this deployment is supposed to make
  selector:
    matchlabels:
      component: web
  template: 						# Configuration for every single Pod using this deployment
    metadata:
      labels:
        component: web
    spec:
      containers:
        - name: client
          image: muarshad01/multi-client
          ports:
            - containerPort: 3000
```

***

## 194: Walking Through the Deployment Config

***

## 195: Applying a Deployment

```
$ kubectl get pods
```

* Remove an object
```
$ kubectl delete -f client-pod.yaml
```
-- pod "client-pod" deleted

```
$ kubectl apply -f client-deployment.yaml
```
-- deployment.apps/client-deployment created

```
$ kubectl get pods
```

```
$ kubectl get deployments
```

```
NAME    DESIRED   CURRENT   UP-TO-DATE    AVAILABLE   AGE
```

***

## 196: Why Use Services?

```
$ minikube ip						    # VM IP adress
$ kubectl get pods -o wide				# Every Pod is assigned its own IP address
```

***

## 197: Scaling and Changing Deployments

```
ports:
  - containerPort: 9999                 # Change port from 3000 to 9999
```

```
$ kubectl apply -f client.deployment.yaml
```
-- deployment.apps/client-deployment configured 		# Notice 'configured' vs 'created'

```
$ kubectl get deployments
$ kubectl get pods
$ kubectl describe pods
```
--replicas: 5

```
$ kubectl apply -f client-deployment.yaml
$ kubectl get pods
```
--image: stephengrider/multi-worker

```
$ kubectl apply -f client-deployment.yaml
$ kubectl get pods
```

***

## 198: Updating Deployment Images

```
$ kubectl apply -f client-deployment.yaml
$ minikube ip
```

***

## 199: Rebuilding the Client Image

```
$ cd complex/client
```

* Crate a new Image

```
$ docker buid -t muarshad01/multi-client . 		# '.' is build context
```

```
* Push to docker hub
$ docker push muarshad01/multi-client
```

***

## 200: Triggering Deployment Updates

`https://github.com/kubernetes/kubernetes/issues/33664`

```
$ kubectl apply -f client-deployment.yaml
```
-- deployment.apps/client-deployment unchanged 		# Notice 'unchanged', it means new config file is same as previous

```
$ kubectl get pods
$ kubectl get deployments
```

***

## 201: Imperatively Updating a Deployment's Image

* Update the multi-client image
* Tag the image with a version number, push to docker hub
* Run a `kubectl` command forcing the deployment to user the new image version

```
$ cd complex/client

$ docker build -t muarshad01/multi-client:v5 .
$ docker push <tag-name>
$ docker push muarshad01/multi-client:v5

$ kubectl set image <object_type>/<object_name> <container_name>=<new image to use>
$ kubectl set image deployment/client-deployment client=muarshad01/multi-client:v5
```
--deployment.extensions/client-deployment image updated

```
$ kubect get pods
$ minikube ip
```

***

## 202: Reminder for Docker Desktop's `Kubernetes` Users

***

## 203: Multiple-Docker Installations

```
$ docker ps
```

***

## 204: Reconfiguring Docker CLI

```
$ cd simplek8s
$ docker ps
$ eval $(minikube docker-env) 			# Temprarily, reconfigures local copy of docker client to 
                                		# connect to the docker server inside the kubernetes node
$ docker ps
```

***

## 205: Why Mess with Docker in the Node?

* Use all the same debugging techniques we learned with Docker CLI
* Manually kill containers to test `Kubernetes` ability to 'self-heal'
* Deleted cached images in the node

```
$ docker logs CONTAINER-ID  
$ docker exec -it CONTAINER-ID sh 			# 'i' STDIN of process; -t: readable format 

$ kubectl get pods
$ kubect logs <pod-name>
$ kubect exec -it <pod-name> sh
$ ls

$ docker system prune -a 
```
