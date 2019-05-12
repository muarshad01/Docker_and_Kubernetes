## Docker and Kubernetes: The Complete Guide  
https://www.udemy.com/docker-and-kubernetes-the-complete-guide/

## How to remove images with '\<none\>'?
docker image ls -a | grep '^\<none\>' | awk '{print $3}' | xargs docker rmi

## Kubernetes commands:

minikube start

minikube status

kubectl cluste-info

kubectl apply -f <file-name.yaml>

kubectl get pods

kibectl get services
