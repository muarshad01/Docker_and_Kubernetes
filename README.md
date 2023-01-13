## Course Link

* [Docker and Kubernetes: The Complete Guide](https://www.udemy.com/docker-and-kubernetes-the-complete-guide/)

## Lectures

* [1_Dive_Into_Docker](https://github.com/muarshad01/Docker-and-Kubernetes/blob/master/1_Dive_Into_Docker.md)
* [2_Manipulating_Containers_with_the_Docker](https://github.com/muarshad01/Docker-and-Kubernetes/blob/master/2_Manipulating_Containers_with_the_Docker.md)
* [3_Building_Custom_Images_Through_Docker_Server](https://github.com/muarshad01/Docker-and-Kubernetes/blob/master/3_Building_Custom_Images_Through_Docker_Server.md)
* [4_Making_Real_Projects_with_Docker](https://github.com/muarshad01/Docker-and-Kubernetes/blob/master/4_Making_Real_Projects_with_Docker.md)
* [5_Docker_Compose_with_Multiple_Local_Containers](https://github.com/muarshad01/Docker-and-Kubernetes/blob/master/5_Docker_Compose_with_Multiple_Local_Containers.md)
* [6_Production_Grade_Workflow](https://github.com/muarshad01/Docker-and-Kubernetes/blob/master/6_Production_Grade_Workflow.md)
* [7_Continuous_Integration_and_Deployment](https://github.com/muarshad01/Docker-and-Kubernetes/blob/master/7_Continuous_Integration_and_Deployment.md)
* [8_Building_a_multi_container_application](https://github.com/muarshad01/Docker-and-Kubernetes/blob/master/8_Building_a_multi_container_application.md)
* [9_Dockerizing_multiple_services](https://github.com/muarshad01/Docker-and-Kubernetes/blob/master/9_Dockerizing_multiple_services.md)
* [10_A_continuous_integration_workflow_for_images](https://github.com/muarshad01/Docker-and-Kubernetes/blob/master/10_A_contious_integration_workflow_for_images.md)
* [11_Multi-Container_Deployments_to_AWS](https://github.com/muarshad01/Docker-and-Kubernetes/blob/master/11_Multi-Container_Deployments_to_AWS.md)
* [12_Onwards_to_Kubernetes](https://github.com/muarshad01/Docker-and-Kubernetes/blob/master/12_Onwards_to_Kubernetes.md)
* [13_Maintaining_Sets_of_Containers_with_Deployments](https://github.com/muarshad01/Docker-and-Kubernetes/blob/master/13_Maintaining_Sets_of_Containers_with_Deployments.md)
* [14_A_Multi-Container_App_with_Kubernetes](https://github.com/muarshad01/Docker-and-Kubernetes/blob/master/14_A_Multi-Container_App_with_Kubernetes.md)
* [15_]
* [16_Kubernetes_Production_Deployment](https://github.com/muarshad01/Docker-and-Kubernetes/blob/master/16_Kubernetes_Production_Deployment.md)
* [17_]
* [18_]

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
