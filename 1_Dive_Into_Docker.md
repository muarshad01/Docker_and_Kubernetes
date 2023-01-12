# Dive into Docker

## 3: Why use Docker?

* Docker makes it really easy to **install and run software** without worrying about **setup or dependencies**.

## 4: What is Docker?

* Docker is a platform or ecosystem around creating and running containers.
* Image: Single file with all the dependencies and configurations required to run a program.
* Container: Instance of an image. Runs a program. **A CONTAINER IS A PROGRAM WITH ITS OWN ISOLATED SET OF HARDWARE RESOURCES.**

## 5: Docker for Mac

* Docker Client (Docker CLI) 
* Docker Server (Docker Daemon)

## 6: Installing Docker on MacOS

* Goto `http://www.docker.com/get-started` 
* Get Started
* Download for Mac 
* Install

```
$ docker version
$ docker run hello-world
```

## 9: Using the Docker Client

```
$ docker run hello-world 
```

* Docker client 
* docker server (deamon) 
* docker HuB
* pull the image 
* create a new Container which runs the executable 
* stream the output to Docker client

## 10: But Really...What's a Container?

* OS: Process --> System Call --> Kernel --> Hardware resources
* OS feature: Name Spacing
* Namespacing: Isolating resources per process (or group of processes)
* Control Groups (`cgroups`): Limit amount of resources used per process
* A CONTAINER IS A PROCESS OR SET OF PROCESSES THAT HAVE A GROUPING OF RESOURCES SPECIFICALLY ASSIGNED TO IT.

## 11: How's Docker Running on Your Computer

