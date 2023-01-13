## 212: The Path to Production

$ cd simplek8s

- Crate config files for each Service and Deployment
- Test locally on minikube
- Create a Github/Travis flow to build images and deploy 
- Deploy app to a cloud provider

Services:
 - Ingress Service
 - ClusterIP Service

 Deployment:
 - multi-client
 - multi-server
 - multi-Worker
 - Redis pod 
 - Postgres pod 

Postgres Persistent Volume Claim: PVC

## 213: Checkpoint Files

## 214: A Quick Checkpoint

$ cd complex
$ docker ps 
$ docker-compose up --build 				# rebuild all images
$ docker-compose up 

localhost:3050

Next: Take all images and migreate them in the world of kubernetes

## 215: Recreating the Deployment

$ cd complex

delete:
- .travis.yml
- docker-compose.yml
- Dockerrun.aws.json
- nginx foldr and we'll be usign IngresServer for routing

$ mkdir k8s
$ cd k8s
$ touch client-deployment.yaml

---
apiVersion: app/v1
kind: Deployment
metadata:
    name: client-deployment
spec:
    replicas: 3
    selector:
        matchLabels: 
            component: web
    template: 						# Node template
        matadata:
            label:
                component: web
        spec:
            containers:
                - name: client
                  image: marshad1/multi-client
			- containerPort: 3000
---

Lecutre 216: NodePort vs ClusterIP Services

Pods:			# Runs one or more closeley related containers 

Services: 		# Sets up 'networking' in a Kubernetes Cluster
- ClusterIP 		# Exposes a set of pods to other objects in the cluster
- NodePort 		# Expose a set of pods to the outside world (only good for dev purposes!!!)
- LoadBalancer
- Ingress

## 217: The ClusterIP Config

$ cd k8s
$ touch client-cluster-ip-service.yaml

---
apiVersion: v1
kind: Service
metadata:
    name: client-cluster-ip-service
spec: 
    type: ClusterIP
    selector:
        component: web
    ports:
        - port: 3000
          targetPort: 3000
---

## 218: Applying Multiple Files with Kubectl

$ kubectl get deployments
$ kubectl deleted deployment client-deployment
$ kubectl get deployments

-- No resources found.

$ kubectl get services
$ kubectl delete service client-node-port
$ kubectl get services

$ kubectl apply -f k8s/client-deployment.yaml
$ kubectl apply -f k8s # applies every config file

$ kubectl get deployments
$ kubectl get services

## 219: Express API Deployment Config

$ cd k8s
$ touch server-deployment.yaml

---
apiVersion: app/v1
kind: Deployment
metadate:
    name: server-deployment
spec:
    replicas: 3
    selector:
        matchLabels:
            component: server
    template:
        metadata:
            labels:
                component: server
        spec:
            containers:
                - name: server
                  image: marshad1/multi-server
                  ports:
                    - containerPort: 5000
---

## 220: Cluter IP for Express API

$ cd k8s
$ touch server-cluster-ip-service.yaml

---
apiVersion: v1
kind: Service
metadata:
    name: server-cluster-ip-service
spec:
    type: ClusterIP
    selector:
        component: server
    ports:
        - port: 5000
          targetPort: 5000
---

## 221: Combining Config Into Single Files

Combine all configuration files together and seperate them with '---'

## 222: The Worker Deployment

$ cd k8s
$ touch workder-deployment.yaml

---
apiVersion: apps/v1
kind: Deployment
metadata: 
    name: worker-deployment
spec:
    replicas: 1
    selector:
        matchLabels:
            component: worker
    template:
        metadate:
            labels:
                component: worker
        spec:
            containers:
                - name: worker
                  image: marshad1/multi-worker
---

## 223: Reapplyig a Batch of Config Files

$ cd complex
$ cd k8s
$ ls k8s
- client-deployment.yaml
- server-deployment.yaml
- worker-deployment.yaml
- client-cluster-ip-service.yaml
- server-cluster-ip-service.yaml

$ kubectl apply -f k8s/
$ kubectl get pods
$ kubectl logs <name-of-pod>

## 224: Creating and Applying Redis Config

$ cd k8s
$ touch redis-deployment.yaml

---
apiVersion: app/v1
kind: Deployment
metadate:
    name: redis-deployment
spec:
    replicas: 1
    selector:
        matchLabels:
            component: redis
    template:
        metadata:
            labels:
                component: redis
        spec:
            containers:
                - name: redis
                  image: redis
                  ports:
                    - containerPort: 6379
---

$ touch redis-cluster-ip-service.yaml

---
apiVersion: v1
kind: Service
metadate:
    name: redis-cluster-ip-service
spec:
    type: ClusterIP
    selector:
        component: redis
    ports:
        - port: 6379
          targetPort: 6379
---

$ cd k8s
$ kubctl apply -f k8s/

$ kubectl get pods
$ kubectl get services

## 225: Important Note about Expected Postgres Error

## 226: Last Set of Boring Config

$ postgres-deployment.yaml

---
apiVersion: apps/v1
kind: Deployment
metadata:
    name: postgres-deployment
spec:
    replicas: 1
    selector:
        matchLabels:
            component: postgres
    template:
        metadata:
            labels:
                component: postgres
        spec:
            containers
            - name: postgres
              image: postgres
              ports:
                - containerPort: 5432
---

$ touch postgres-cluster-ip-service.yaml

---
apiVersion: v1
kind: Service
metadate:
    name: postgres-clustre-ip-service
spec:
    type: ClusterIP
    selector:
        component: postgres
    ports:
        - port: 5432
          targetPort: 5432
---

$ cd k8s
$ kubectl apply -f k8s/
$ kubectl get pods
$ kubectl get service

## 227: The Need for Volumes with Databases

Persistent Volume Claim (PVC)

## 228: Kubernetes Volumes

- Persistent Volume Claims
- Persistent Volume
- Volume

Volume is tied to Pod. So, if a Pod itself ever dies the volume dies and goes away as well. 
Volume will, however, survive the container restarts.

## 229: Volumes vs Persistent Volumes

Persistent volume is not tied to any 'pod' or 'container'

Pod {
    Container{...}
}

Persistent volume is outside the pod.

## 230: Persistent Volumes vs Persistent Volume Claims

Persistent Volume Claim (PVC) is like a 'Billboard' for advertisement of options you ask for in 'pod' config.

Statistically provisioed Persistent Volume
Dynamically provisioned Persistent Volume

## 231:  Claim Config Files

```
$ cd k8s
$ touch database-persistent-volume-claim.yaml
```

```
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
    name: database-persistent-volume-claim
spec:
    accessModes:
        - ReadWriteOnce
    resources:
        requestes:
            storage: 2Gi
```

==
## 232: Persistent Volume Access Modes
==

ReadWriteOnce			# Can be used by a single node.
ReadOnlyMany			# Multiple nodes can read from this.
ReadWriteMany			# Can be read and written to by many nodes.

## 233: Where Does Kubernetes Allocate Persistent Volumes

```
$ kubectl get storageclass

NAME    PROVISIONER     AGE

$ kubectl describe storageclass
```

## 234: Designating a PVC in a Pod Template

```
apiVersion: apps/v1
kind: Deployment
metadata:
    name: postgres-deployment
spec:
    replicas: 1
    selector:
        matchLabels:
            component: postgres
    template:
        metadata:
            labels:
                component: postgres
        spec:
        volumes:
            - name: postgres-storage
              persistentVolumeClaim: database
                claimName: database-persistent-volume-claim
            containers
            - name: postgres
              image: postgres
              ports:
                - containerPort: 5432
              volumeMounts:
                - name: postgres-storage 				# Notice: volume name and volumeMount name are the same
                  mountPath: /var/lib/postgresql/data 
                  subPath: postgres 
```

## 235: Applyng a PVC

```
$ cd k8s
$ kubectl apply -f k8s
$ kubectl get pods
$ kubectl get pv 						# pv: persistent volume
```

## 236: Defining Environment Variables

## 237: Adding Environment Variables to Config

```
apiVersion: apps/v1
kind: Deployment
metadata: 
    name: worker-deployment
spec:
    replicas: 1
    selector:
        matchLabels:
            component: worker
    template:
        metadata:
            labels:
                component: worker
        spec:
            containers:
                - name: worker
                  image: marshad1/multi-worker
                  env:
                    - name: REDIS_HOST
                      value: redis-cluster-ip-service
                    - name: REDIS_PORT
                      value: 6379
```

```
apiVersion: app/v1
kind: Deployment
metadate:
    name: server-deployment
spec:
    replicas: 3
    selector:
        matchLabels:
            component: server
    template:
        metadata:
            labels:
                component: server
        spec:
            containers:
                - name: server
                  image: marshad1/multi-server
                  ports:
                    - containerPort: 5000
                  env:
                    - name: REDIS_HOST
                      value: redis-cluster-ip-service
                    - name: REDIS_PORT
                      value: 6379
                    - name: PGUSER
                      value: postgres
                    - name: PGHOST
                      value: postgres-cluster-ip-service
                    - name: PGDATABASE
                      value: postgres
```

=======================================
## 238: Creating an Encoded Secret
=======================================

1. Pods 					# Runs one or more closely related containers

2. Deployments 					# Administers and manages a set of pods

3. Services 					# Sets up networking in a Kubernetes Cluster
    - ClusterIP: Exposes a set of pods to other objects in the cluster
    - NodePort: Exposes a set of pods to the outside world (only good for dev purposes!!!)
    - LoadBalancer
    - Ingress

4. Secrets 					# Securely stores a piece of information in the cluster, such as a database password

Creating a Secret
------------------
$ kubectl create secret generic <secret_name> --from-literal key=value
$ kubectl create secret generic pgpassword --from-literal PGPASSWORD=abcd1234
secret/pgpassword created

$ kubectl get secrets

Type of secret:
- docker-registry
- TLS

## 239: Postgres Environment Variable Fix

## 240: Passing Secrets as Environment Variables

```
$ kubectl get secret

- name: PGPASSWORD
    valueFrom:
        secretKeyRef:
            name: pgpassword
            key: PGPASSWORD

$ kubectl apply -f k8s
```

## 241: Environment Variables as Strings













