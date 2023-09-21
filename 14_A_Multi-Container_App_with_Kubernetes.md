## 206: The Path to Production

```
$ cd simplek8s
```

* Crate config files for each of `kind:Pod`, `kind:Service`, and `kind:Deployment`
* Test locally on `minikube`
* Create a `GitHub/Travis` flow to build images and deploy 
* Deploy app to a cloud provider

* `kind:Service`:
  - `type: Ingress`
  - `type: ClusterIP` # previously we used `NodePorts`

* `kind:Deployment`:
  - multi-client
  - multi-server
  - multi-Worker
  - `Redis` pod 
  - `Postgres` pod 

* Postgres `Persistent Volume Claim (PVC)`

***

## 207: Checkpoint Files

```
$ docker-compose -f docker-compose-dev.yml up
$ docker-compose -f docker-compose-dev.yml up --build
$ docker-compose -f docker-compose-dev.yml down
```

***

## 208: A Quick Checkpoint

```
$ cd complex

$ docker ps 
$ docker-compose up --build 				# rebuild all images
$ docker-compose up 
```

```
localhost:3050
```

* Take all images and migrate them in the world of `kubernetes`

***

## 209: Recreating the Deployment

```
$ cd complex
```

* delete:
    - `.travis.yml`
    - `docker-compose.yml`
    - `dockerrun.aws.json`
    - `nginx`                       # folder and we'll be using `Ingres` Service for routing

```
$ mkdir k8s
$ cd k8s
$ touch client-deployment.yaml
```

```
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
                  image: muarshad01/multi-client
			- containerPort: 3000
```

***

## 210: `NodePort` vs `ClusterIP` Services

* `Kind: Pods`                # Runs one or more closely related containers 
* `Kind: Services` 		      # Sets up `networking` in a `Kubernetes` Cluster
    - `type: ClusterIP`       # Exposes a set of pods to other-objects in the cluster
    - `type: NodePort`        # Expose a set of pods to the outside-world
    - `type: LoadBalancer`
    - `type: Ingress`
***

## 211: The `ClusterIP` Config

```
$ cd k8s
```

Create `client-cluster-ip-service.yaml` file

```
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
```

***

## 212: Applying Multiple Files with `Kubectl`

```
$ kubectl get deployments
$ kubectl deleted deployment client-deployment
$ kubectl get deployments
```
-- No resources found.

```
$ kubectl get services
$ kubectl delete service client-node-port
$ kubectl get services

$ kubectl apply -f k8s/client-deployment.yaml
$ kubectl apply -f k8s                          # applies every config file

$ kubectl get deployments
$ kubectl get services
```

***

## 213: Express API Deployment Config

```
$ cd k8s
```

Create `server-deployment.yaml`

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
                  image: muarshad01/multi-server
                  ports:
                    - containerPort: 5000
```

***

## 214: `CluterIP` for Express API

```
$ cd k8s
```

Create `server-cluster-ip-service.yaml`

```
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
```

***

## 215: Combining Config Into Single Files

* Combine all configuration files together and separate them with `---`

***

## 216: The Worker Deployment

```
$ cd k8s
```

Create `worker-deployment.yaml`

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
                  image: muarshad01/multi-worker
```

***

## 217: Reapplying a Batch of Config Files

```
$ cd complex/k8s
$ ls k8s
```

* `client-deployment.yaml`
* `server-deployment.yaml`
* `worker-deployment.yaml`
* `client-cluster-ip-service.yaml`
* `server-cluster-ip-service.yaml`

```
$ kubectl apply -f k8s/
$ kubectl get pods
$ kubectl logs <name-of-pod>
```

***

## 218: Creating and Applying Redis Config

```
$ cd k8s
```

Create `redis-deployment.yaml`

```
apiVersion: app/v1
kind: Deployment
metadata:
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
```

Create `redis-cluster-ip-service.yaml`


```
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
```

```
$ cd k8s
$ kubctl apply -f k8s/

$ kubectl get pods
$ kubectl get services
```

***

## 219: Important Note about Expected `Postgres` Error

***

## 220: Last Set of Boring Config

Create `postgres-deployment.yaml`


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
            containers
            - name: postgres
              image: postgres
              ports:
                - containerPort: 5432
```

Create `postgres-cluster-ip-service.yaml`

```
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
```

```
$ cd k8s
$ kubectl apply -f k8s/
$ kubectl get pods
$ kubectl get service
```

***

## 221: The Need for Volumes with Databases

`Persistent Volume Claim (PVC)`

***

## 222: Kubernetes Volumes

* `Volume`
* `Persistent Volume`
* `Persistent Volume Claims (PVC)`

* `Volume is Tied-to-Pod`. 
    - So, if a Pod itself ever DIES the `Volume` DIES and goes away as well. 
    - Volume will, however, survive the container restarts.

***

## 223: `Volumes` vs `Persistent Volumes`

* Persistent volume is NOT-tied to any pod-or-container: `Pod {C1, C2, ..., CN}`
    - Persistent volume is outside-the-pod.

***

## 224: `Persistent Volumes` vs `Persistent Volume Claims (PVC)`

* PVC is like a 'Billboard' for advertisement of options you ask for in 'pod' config.
    - Statistically provisioned Persistent Volume
    - Dynamically   provisioned Persistent Volume

***

## 225:  Claim Config Files

```
$ cd k8s
```

Create `database-persistent-volume-claim.yaml`

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

***

## 226: Persistent Volume Access Modes

* `ReadWriteOnce`			# Can be used by a single-node.
* `ReadOnlyMany`			# Multiple-nodes can read from this.
* `ReadWriteMany`			# Can be read-and-written to by many nodes.

***

## 227: Where Does `Kubernetes` Allocate Persistent Volumes

```
$ kubectl get storageclass

NAME    PROVISIONER     AGE

$ kubectl describe storageclass
```

***

## 228: Designating a PVC in a Pod-Template

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

***

## 229: Applying a `PVC`

```
$ cd k8s
$ kubectl apply -f k8s
$ kubectl get pods
$ kubectl get pv 						# pv: persistent volume
```

***

## 230: Defining Environment Variables

***

## 231: Adding Environment Variables to Config

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

***

## 232: Creating an Encoded Secret

1. `kind: Pods` 					        # Runs one-or-more closely related containers

2. `kind: Deployments` 					    # Administers and manages a set-of-pods

3. `Kind: Services` 					    # Sets up `networking` in a `Kubernetes` Cluster
    - `type: ClusterIP`                     # Exposes a set of pods to other-objects-in-the-cluster
    - `type: NodePort`                      # Exposes a set of pods to the outside-world
    - `type: LoadBalancer`
    - `type: Ingress`

4.`kind:Secrets` 					        # Securely stores a piece of information in the cluster, such as a database password

### Creating a Secret

```
$ kubectl create secret generic <secret_name> --from-literal key=value
$ kubectl create secret generic pgpassword    --from-literal PGPASSWORD=abcd1234
```
--secret/pgpassword created

```
$ kubectl get secrets
```

***

## 233: `Postgres` Environment Variable Fix

***

## 234: Passing Secrets as Environment Variables

```
$ kubectl get secret
```

```
- name: PGPASSWORD
    valueFrom:
        secretKeyRef:
            name: pgpassword
            key: PGPASSWORD

$ kubectl apply -f k8s
```

***
## 235: Environment Variables as Strings
