# Dive into Docker

***

## 3: Why use Docker?

* Docker makes it really easy to **Install and Run Software** without worrying about **Setup or Dependencies**.

***

## 4: What is Docker?

* Docker is a **platform or ecosystem** around creating and running containers.
* Image is a single file with all the dependencies and configurations required to run a program.
* Container: 
  - Instance of an image. 
  - Runs a program. 
  - $\color{red}{A\ container\ is\ a\ PROGRAM\ with\ its\ own\ ISOLATED\ set\ of\ hardware\ resources.}$

***

## 5: Docker for MacOS

* Docker Client (Docker CLI) 
* Docker Server (Docker Daemon)

***

## 6: Installing Docker on MacOS

* `http://www.docker.com/get-started` 
* Get Started
* Download for Mac 
* Install

```
$ docker version
$ docker run hello-world
```

***

## 9: Using the Docker Client

```
$ docker run hello-world 
```

* Docker client 
* docker server (daemon) 
* docker Hub
* pull the image 
* create a new Container which runs the executable 
* stream the output to Docker client

***

## 10: But Really...What's a Container?

* OS
  - Process 
  - System Call 
  - Kernel 
  - Hardware resources

* Namespacing 
  - OS feature
  - Isolating resources per process (or group-of-processes)
  - Control Groups (`cgroups`): Limit amount of resources used per process
* $\color{red}{A\ container\ is\ a\ process\ or\ set\ of\ processes\ (i.e., program)\ that\ have\ a\ GROUPING\ OF\ RESOURCES\ specifically\ assigned\ to\ it.}$

***

## 11: How's Docker Running on Your Computer?

***
