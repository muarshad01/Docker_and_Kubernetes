
## 171: The Why's and What's of `Kubernetes`

* `AWS Elastic Beanstalk (EBS)` application has following 4 servers / containers:
  - nginx
  - client
  - server
  - worker

* Question: How would we **Scale-up** this application?
    - a lot of users
    - a lot of traffic coming in

* Question: What is `Kubernetes`? 
  - System for running many different containers over multiple different machines

* Q: Why use `Kubernetes`? 
  - When you need to run many different containers with different images

* A **Cluster** in k8s world is: 
    - 1 set of master `Master` and  
    - 1 or more `Nodes`
    - A `Node` is a VM-or-Physical-machine that runs one or more containers

* Load Balancer --> `Master` --> {Node1, Node2, ...}

`dockerrun.aws.json` spins-up an entire set of containers, i.e., EB

***

## 172: `Kubernetes` in Development and Production

* `Kubernetes`:
    - **Development**:
	    - `minikube` (a program that sets up `k8s` on local computer)
    - **Production (Managed solutions)**:
        - `EKS`: Amazon Elastic Container Service for `Kubernetes`
        - `GKE`: Google Cloud `Kubernetes` Engine
 
* `minikube` 
    - Creates a `K8s` cluster locally (single `Node`)
    - VM {Node {C1, C2, ..., CN}}
* `kubectl`  
    - Used for managing containers in the Node on both local / prod environments
    -  VM {Node {C1, C2, ..., CN}}

* Local `Kubernetes` Development:
    - Install `kubectl`
    - Install a VM driver `virtualbox`
    - Install `minikube`


***

## 173: Docker Desktop's `Kubernetes` Setup and Installation - `macOS`

* Docker Desktop -> Settings -> `Kubernetes` -> Enable Kubernetes -> Apply & Restart

```
$ kubectl version
```

***

## 174: Docker Desktop's `Kubernetes` Setup and Installation - Windows

***

## 175: Updated `minikube` Install and Setup -- `MacOS`

* If you are already using Docker Desktop's `Kubernetes`, you do not need `Minikube` and these instructions can be skipped.

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

### Starting `minikube` and Testing Installation

#### Start with VM driver

```
$ minikube start --driver=hyperkit
$ brew install hyperkit
```

#### Check `Minikube` Status

```
$ minikube status
```

#### Check `kubectl`

```
$ kubectl version
```

***

## 176: `minikube` Setup on MacOS

### 1. Make sure `brew` is installed

`https://brew.sh`	

```
$ which brew
/opt/homebrew/bin/brew
```

### 2. Install `kubectl`

```
$ brew install kubectl
$ which kubectl
# /usr/local/bin/kubectl
/Users/marshad/.docker/bin/kubectl
```

### 3. Install a VM driver `virtualbox`

`https://www.virtualbox.org/` -> Downloads -> macOS)

### 4. Install `minikube`

```
$ brew cask install minikube
$ which minikube
/usr/local/bin/minikube
```

### 5. Start minikube

```
$ minikube start
$ minikube status

```

***

## 177: `minikube` Setup on Windows

***

## 178: `minikube` Setup on Linux

***

## 179: Mapping Existing Knowledge

```
$ minikube status
```

- minikube: Running
- cluster: Running
- kubectl: Correctly Configured: pointing to ...

```
$ kubectl cluster-info
```

- `Kubernetes master` is running at `https://192.168.64.2:8443`
- `KubeDNS` is running at `https://192.168.64.2:8443/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy`

***

## 180: Quick Note to prevent an Error

***

## 181: Adding Configuration Files

* `https://hub.docker.com`
* `Kubernetes` expects ALL images to already be built

* Kind
    - service
    - pod

```
$ mkdir simpleK8s
$ cd simpleK8s
```

Create file `client-pod.yaml`


```
apiVersion: v1			 	# Scopes or limits the type of 'objects' we can create with any given configuration
kind: Pod
metadata:
  name: client-pod
  labels:
    component: web 			# related or tied to other config file that we will write down
spec:
  containers:
    - name: client
      image: muarshad01/multi-client 	    # image on docker hub
      ports:
        - containerPort: 3000
```


Create `client-node-port.yaml`

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

***

## 182: Object Types and API Versions

In `kubernetes`, config files are used to create `Objects` (or specific types of objects), 
a thing that exists inside our kubernetes cluter.

* Example Object types:
    - `StatefulSet`
    - `ReplicaController`
    - `kind`
        - `Pod`: A Pod object is used to "run a container".
        - `Service`: A Service object is used to "set up some-kind-of-networking".

Each API version defines a different set of 'objects' we can use

***

## 183: Running Containers in Pods

```
$ minikube start 			# Create a VM referred to as 'Node'; 
                   			# Node runs various types of 'objects' such as Pod
                   			# Pod is a "grouping of containers" with similar purpose
```

```
Node{
  Pod {
    nginx: container
  }
}
```

***

## 184: Service Config Files in Depth

Object Types:

* Kind
    - `Pod`{C1, C2, ..., CN}
        - Runs one or more closely-related-containers
    - `Services`: Sets up **networking** in a k8s cluster. 
    - There are 4 subtypes:
	    - `ClusterIP`
	    - `NodePort`: Exposes a container to the outside world
	    - `LoadBalancer`
	    - `Ingres`

```
apiVersion: v1
kind: Service 			        # 'Kind' represents the type of `object` that we want to make
metadata:
  name: client-node-port
spec:
  type: NodePort 		        # Subtype of `Service` object
  ports:
    - port: 3050  		        # Other Pod that needs to communicate with multi-client Pod
      targetPort: 3000 		    # Same as `containerPort` in Pod definition
      nodePort: 31515 		    # We type this into our browser URL [30,000 -- 32,767]
  selector:
    component: web 		        # Note the relationship with `label` in other service.
```

* `Port Forwarding`: `label` and `selector` with key:value approach. 
    - labels and selectors are used to connect objects.

```
* labels:
    - component: web
* selector:
    - component: web

OR

* labels:
    - tier: frontend
* selector:
    - tier: frontend
```

***

## 185: Connecting to Running Containers

```
$ cd simplek8s
```

```
$ kubectl apply -f client-pod.yaml 
```
-- pod/client-pod configured

```
$ kubectl apply -f client-node-port.yaml
```
-- service/client-node-port configured

```
$ kubectl get pods
```

NAME        READY   STATUS    RESTARTS    AGE
client-pod  1/1     Running   0           1h

```
$ kubectl get services
```

NAME              TYPE        CLUSTER-IP      EXTERNAL-IP     PORTS(S)        AGE
client-node-port  NodePort    a.b.c.d         <none>          3050:31515/TCP  1h
kubernetes        ClusterIP   p.q.r.s         <none>          443/TCP         4d

```
$ minikube ip 					# prints the ip address of that VM
```

`https://<VM ip-address>:nodePort`

***

## 186: The Entire Deployment Flow

```
$ kubectl get pods
$ docker ps
$ docker kill CONTAINER_ID
$ docker ps
$ kubectl get pods
```

NAME        READY   STATUS    RESTARTS    AGE
                                 1           
* **Note**: Deployment file goes to `Master` and not `Nodes`

```
$ kubectl apply -f client-node-port.yaml
```


***

## 187: Imperative vs Declarative Deployments

* `Kubernetes` is a system to deploy containerized apps
* `Nodes` are individual machines (or VM's) that run containers: Node{C1, C1, ...}
* `Masters` are machines (or VM's) with a set of programs to manage Nodes
* `Kubernetes` didn't build our imgages - it got them from somewhere else
* `Kubernetes` (the master) decides where to run each container - each Node 
  can run a dissimilar set of containers
* To deploy something, we update the desired state of the master with a config file
* The `master` works constantly to meet your desired state

***
