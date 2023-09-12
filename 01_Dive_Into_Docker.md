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

```
To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (arm64v8)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
```

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
