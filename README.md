#############
#Installation
#############

1. Goto http://www.docker.com/get-started --> Get Started --> Download for Mac --> Install



## Docker and Kubernetes: The Complete Guide  
https://www.udemy.com/docker-and-kubernetes-the-complete-guide/

$ docker system prune -a <br />
$ docker image ls -a <br />

## How to remove images with '\<none\>'?
docker image ls -a | grep '^\<none\>' | awk '{print $3}' | xargs docker rmi

## Kubernetes commands:

minikube start <br />
minikube status <br />
minikube ip <br />

kubectl cluste-info <br />
kubectl apply -f <file-name.yaml> <br />
kubectl get pods <br />
kubectl get pods -o wide <br />
kubectl get services <br />

kubectl describe <object-type> <object-name> <br />
kubectl describe pod clien-pod <br />

kubectl delete -f <config file> <br />
kubectl get deployments <br />
kubectl set image <object_type> / <object_name> <container_name> = <new_image_to_use> <br />
kubectl set image deployment/client-deployment client=marshad1/multi-client:v5 <br />

Configure VM to use Your Docker Server: eval $(minikube docker-env) 
