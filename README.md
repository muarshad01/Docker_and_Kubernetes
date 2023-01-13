## Course Link

* [Docker and Kubernetes: The Complete Guide](https://www.udemy.com/docker-and-kubernetes-the-complete-guide/)

## Installation

* `http://www.docker.com/get-started` 
* Get Started
* Download for Mac 
* Install

```
$ docker system prune -a
$ docker image ls -a
```

## How to remove images with `\<none\>`?

```
$ docker image ls -a | grep '^\<none\>' | awk '{print $3}' | xargs docker rmi
```

## Docker commands

```
```

## Kubernetes commands

```
$ minikube start
$ minikube status
$ minikube ip

$ kubectl cluste-info
$ kubectl apply -f <file-name.yaml>
$ kubectl get pods
$ kubectl get pods -o wide
$ kubectl get services

$ kubectl describe <object-type> <object-name>
$ kubectl describe pod clien-pod

$ kubectl delete -f <config file>
$ kubectl get deployments
$ kubectl set image <object_type> / <object_name> <container_name> = <new_image_to_use>
$ kubectl set image deployment/client-deployment client=marshad1/multi-client:v5
```

Configure VM to use Your Docker Server `eval $(minikube docker-env)`
