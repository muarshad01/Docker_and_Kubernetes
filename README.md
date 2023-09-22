## Course Link

* [Docker and Kubernetes: The Complete Guide](https://www.udemy.com/docker-and-kubernetes-the-complete-guide/)

***

### Docker

* [01: Dive Into Docker](https://github.com/muarshad01/Docker-and-Kubernetes/blob/master/01_Dive_Into_Docker.md)
* [02: Manipulating Containers with the Docker](https://github.com/muarshad01/Docker-and-Kubernetes/blob/master/02_Manipulating_Containers_with_the_Docker.md)
* [03: Building Custom Images Through Docker Server](https://github.com/muarshad01/Docker-and-Kubernetes/blob/master/03_Building_Custom_Images_Through_Docker_Server.md)
* [04: Making REAL Projects with Docker](https://github.com/muarshad01/Docker-and-Kubernetes/blob/master/04_Making_Real_Projects_with_Docker.md)
* [05: Docker Compose with Multiple Local Containers](https://github.com/muarshad01/Docker-and-Kubernetes/blob/master/05_Docker_Compose_with_Multiple_Local_Containers.md)
* [06: Production Grade Workflow](https://github.com/muarshad01/Docker-and-Kubernetes/blob/master/06_Production_Grade_Workflow.md)
* [07: Continuous Integration and Deployment](https://github.com/muarshad01/Docker-and-Kubernetes/blob/master/07_Continuous_Integration_and_Deployment.md)
* [08: Building a Multi-Container Application](https://github.com/muarshad01/Docker-and-Kubernetes/blob/master/08_Building_a_multi_container_application.md)
* [09: Dockerizing Multiple Services](https://github.com/muarshad01/Docker-and-Kubernetes/blob/master/09_Dockerizing_multiple_services.md)
* [10: A Continuous Integration Workflow for Images](https://github.com/muarshad01/Docker-and-Kubernetes/blob/master/10_A_contious_integration_workflow_for_images.md)
* [11: Multi-Container Deployments to AWS](https://github.com/muarshad01/Docker-and-Kubernetes/blob/master/11_Multi-Container_Deployments_to_AWS.md)

***

### Kubernetes

* [12: Onwards to Kubernetes](https://github.com/muarshad01/Docker-and-Kubernetes/blob/master/12_Onwards_to_Kubernetes.md)
* [13: Maintaining Sets of Containers with Deployments](https://github.com/muarshad01/Docker-and-Kubernetes/blob/master/13_Maintaining_Sets_of_Containers_with_Deployments.md)
* [14: A Multi-Container App with Kubernetes](https://github.com/muarshad01/Docker-and-Kubernetes/blob/master/14_A_Multi-Container_App_with_Kubernetes.md)
* [15: Handling Traffic with Ingress Controllers](https://github.com/muarshad01/Docker-and-Kubernetes/blob/ch15/15_Handling_Traffic_with_Ingress_Controllers.md)
* [16: Kubernetes Production Deployment](https://github.com/muarshad01/Docker-and-Kubernetes/blob/master/16_Kubernetes_Production_Deployment.md)
* [17: HTTPS Setup with Kubernetes](https://github.com/muarshad01/Docker-and-Kubernetes/blob/chap17/17_HTTPS_Setup_with_Kubernetes.md)
* [18: Local Development with Skaffold](..)

***

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

* Configure VM to use Your Docker Server 

```
$ eval $(minikube docker-env)
```

***

## Notes

```
* Kind: `Pod`                   # Runs one-or-more closely related containers
* Kind: `Deployment`            # Administers and manages a set-of-pods
* Kind: `Service`               # Sets up networking in a Kubernetes Cluster
    - `type: ClusterIP`               # Exposes a set of pods to other-objects-in-the-cluster
    - `type: NodePort`                # Exposes a set of pods to the outside-world
    - `type: LoadBalancer`
    - `type: Ingress`
* kind: `Secrets`
```

* `labels` vs `selectors`
