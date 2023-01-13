## 177: The Why's and What's of Kubernetes

* AWS Elastic Bean Stalk (EBS) has following 4 servers / containers:
  - nginx
  - server
  - client
  - worker

* Q: How would we SCALE-UP this application?

* Q: What is Kubernetes? 
  - System for running many different-containers over multiple-different machines

* Q: Why use Kubernetes? 
  - When you need to run many different-containers with different-images


* A CLUSTER in k8s world is ONE set of MASTER and a bunch of other NODES {A VM or Physical machine that runs one or more containers!}

* Load Balancer --> Master --> {Node1, Node2, ...}

## 178: Kubernetes in Development and Production

Kubernetes:
-- Development:
	MiniKube (CMT line tool: sets up a tiny-little-cluster on local computer)
-- Production (Managed solutions):
	EKS: Amazon Elastic Container Service for Kubernetes (EKS)
    	GKE: Google Cloud Kubernetes Engine
 
minikube (Used for managing VM itself)               --> Virtual Machine {Node {Container_1, ..., Container_N}}
-- local only
kubectl  (Used for managing containers in the Node)  --> Virtual Machine {Node {Container_1, ..., Container_N}}
-- local/production Env

Local Kubernetes Development:
- Install kubectl
- Install a VM driver virtualbox
- Install minikube

## 179: Docker Desktop's Kubernetes Setup and Installation - macOS

* Docker Icon --> Preferences --> Kubernetes --> Apply & Restart

## 180: Docker Desktop's Kubernetes Setup and Installation - Windows

## 181: Updated Minikube Install and Setup -- MacOS

```
$ which brew
/usr/local/bin/brew
$ /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

```
$ brew install minikube
$ which minikube
/usr/local/bin/minikube
```

Starting Minikube and Testing Installation:
------------------------------------------
1. Start with VM driver

$ minikube start --driver=hyperkit
$ brew install hyperkit

2. Check Minikube Status
$ minikube status

3. Check kubectl
$ kubectl version

## 182: Minikube Setup on MacOS

* 1. Make sure Brew is installed			# https://brew.sh	
* 2. Install Kubecel				# brew install kubectl
* 3. Install a VM driver virtualbox		# Download VirtualBox @virtualbox.org
* 4. Install minikube				# brew cast install minikube
* 5. Start minikube				# minikube start

```
1.
$ which brew
/usr/local/bin/brew

2.
$ brew install kubectl
$ which kubectl
/usr/local/bin/kubectl

3.
https://www.virtualbox.org/ (Downloads --> OS X hosts)

4.
$ brew cask install minikube
$ which minikube
/usr/local/bin/minikube

5. 
Start minikube --> $ minikube start
                      $ minikube status
```

## 183: Minikube Stup on Windows

## 184: Minikube Stup on Linux

## 185: Mapping Existing Knowledge

```
$ minikube status

- minikube
- type: Control Plane
- host: Running
- kubelet: Running
- apiserver: Running
- kubeconfig: Configured

$ kubectl cluster-info

- Kubernetes master is running at https://192.168.64.2:8443
- KubeDNS is running at https://192.168.64.2:8443/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy

- To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'.
```

## 186: Quick Note to prevent an Error

## 187: Adding Configuration Files

`https://hub.docker.com`

Kubernetes expects ALL images to already be built

```
$ mkdir simpleK8s
$ cd simpleK8s
$ touch client-pod.yaml 		# create a file
```

```
apiVersion: v1			 	# Scopes or limits the type of 'objects' we can create with any given configuration
kind: Pod
metadata:
  name: client-pod
  labels:
    component: web 			# related or tied to other config file we will write down
spec:
  containers:
    - name: client
      image: marshad1/multi-client 	# image on docker hub
      ports:
        - containerPort: 3000
```

```
$ touch client-node-port.yaml 		# create a file
```

```
apiVersion: v1
kind: Service 			# 'Kind' represents the type of object that we want to make e.g., Kind=Service or kind=Pod
metadata:
  name: client-node-port
spec:
  type: NodePort
  ports:
    - ports: 3050
      targetPort: 3000
      nodePort: 31515
  selector:
    component: web
```

## 188: Object Types and API Versions

In kubernetes, config files are used to crate 'Objects' (or specific types of objects), 
a thing that exists inside our kubernetes cluter.

Example object types:
1. StatefulSet
2. ReplicaController
3. kind: Pod - A Pod object is used to "run a container".
4. kind: Service - A Service object is used to "set up some kind of networking".

Each API version defines a different set of 'objects' we can use

## 189: Running Containers in Pods

```
$ minikube start 			# Create a VM referred to as 'Node'; 
                   			# Node runs various types of 'objects' such as Pod
                   			# Pod is a "grouping of containers" with similar purpose

Node{
  Pod {
    nginx: container
  }
}
```

## 190: Service Config Files in Depth

Object Types:

Kind: Pod {C1, ...CN}: Runs one or more closely related containers

Kind: Services: Sets up networking in a Kubernetes cluster. There are 4 subtypes:
	-- ClusterIP
	-- NodePort: Exposes a container to the outside world (only good for 'dev' purposes!!!)
	-- LoadBalancer
	-- Ingres

```
apiVersion: v1
kind: Service 			# 'Kind' represents the type of object that we want to make e.g., Kind=Service or kind=Pod
metadata:
  name: client-node-port
spec:
  type: NodePort 		# Subtype of Service object; exposes a container to the outside world
  ports:
    - port: 3050  		# OTHER Pod that needs multi-client Pod
      targetPort: 3000 		# same as containerPort in kind=Pod definition
      nodePort: 31515 		# We type this into our browser URL [30,000 -- 32,767]
  selector:
    component: web 		# Note the relationship with label in other service. How they are connected together.
```

Port Forwarding: "label" and "selector" with key:value approach. labels and selectors are used to connect objects.

```
labels:
    component: web
selector:
    component: web

OR

labels:
    tier: frontend
selector:
    tier: frontend
```

## 191: Connecting to Running Containers

```
$ cd simplek8s

$ kubectl apply -f <file-name>
$ kubectl apply -f client-pod.yaml 
-- pod/client-pod configured

$ kubectl apply -f client-node-port.yaml
-- service/client-node-port configured

$ kubectl get pods

NAME        READY   STATUS    RESTARTS    AGE
client-pod  1/1     Running   0           1h

READY x/y = READY Number-pods/Number of copies runnies

$ kubectl get services

NAME              TYPE        CLUSTER-IP      EXTERNAL-IP     PORTS(S)        AGE
client-node-port  NodePort    a.b.c.d         <none>          3050:31515/TCP  1h
kubernetes        ClusterIP   p.q.r.s         <none>          443/TCP         4d

PORTS port:NodePort

$ minikube ip 					# prints the ip address of that VM

https://<VM ip-address>:port
```

## 192: The Entire Deployment Flow

```
$ kubectl get pods
$ docker ps
$ docker kill CONTAINER_ID
$ docker ps
$ kubect get pods
```

NAME        READY   STATUS    RESTARTS    AGE
                                 1           
* Note: Deployment file goes to Master and not Nodes

## 193: Imperative vs Declarative Deployments

* Kubernetes is a system to deploy containerized apps
* Nodes are individual machines (or vm's) that run containers
* Masters are machines (or vm's) with a set of programs to manage Nodes
* Kubernetes didn't build our imgages - it got them from somewhere else
* Kubernetes (the master) decided where to run each container - each node 
  can run a dissimilar set of containers
* To deploy something, we update the desired state of the master with a config file
* The master works constantly to meet your desired state
