## 26: Creating Docker Images

* `hello-world`, `redis`, and `busybox` images

* Steps: 
  - `Dockerfile` 
  - Docker Client
  - Docker Server (daemon) 
  - Usable Image

* Creating a `Dockerfile` Flow:
  - (a) Specify a BASE image
  - (b) Run some commands to install ADDITIONAL programs
  - (c) Specify a command to RUN on container startup

***

## 27: `Buildkit` for Docker Desktop

***

## 28: Building a `Dockerfile`

* Create an image that runs a `redis-server`

```
$ mkdir redis-image
$ cd redis-image
$ touch Dockerfile 				# Create a file named `Dockerfile` containing the following
```

```
# 1. Use an existing docker image as a "BASE"
FROM alpine

# 2. Download and install a dependency
RUN apk add --update redis              # apk is a program called "APACHE PACKAGE MANAGER" that comes pre-installed with alpine

# 3. Tell the image what to do when it starts as a container
CMD ["redis-server"]
```

```
4. $ docker build . 				# Successfully built IMAGE_ID
5. $ docker run IMAGE_ID 			# Ready to accept connections
```

***

## 29: `Dockerfile` Teardown

```
FROM alpine                         # Use an existing docker image as a base
RUN apk add --update redis          # Download and install a dependency
CMD ["redis-server"]                # Tell the image what to do when it starts as a container
```

***

## 30: What's a Base Image

Writing a `Dockerfile` == Being given a computer with no OS and being told to install Google Chrome

How do you install `Chrome` on a computer with no OS?

* Specify a base image (`FROM`)
  - Install an OS

* Run commands to install additional programs (`RUN`)
  - Start up your default browser
  - Navigate to chrome.google.com
  - Download installer
  - Open file/folder explorer
  - Execute chrome_installer.exe

* Command to run on startup (`CMD`)
  - Execute chrome.exe

***

## 31: The Build Process in Detail

```
$ docker build .                # Giving our 'Dockerfile' to docker CLI 
                                # '.' specifies the build-context
                                # The set of files/folder that we want in a docker image
```

* [1/2] FROM docker.io/library/alpine@sha256:7144f7bab3d4c2648d7e59409f15ec52a18006a128c733fcff20d3a4a54ba44a
* latest: Pulling from library/apline
* Digest: ...
* Status: Downloaded newer image for alpine:latest

***

## 32: A Brief Recap

***

## 33: Rebuilds with Cache

```
# Use an existing docker image as a base
FROM alpine

# Download and install a dependency
RUN apk add --update redis 					# apk is a program called Apache Package Manager that comes pre-installed with alpine
RUN apk add --update gcc

# Tell the image what to do when it starts as a container
CMD ["redis-server"]
```

* CACHED [2/3] RUN apk add --update redis

***

## 34: Tagging an Image

```
$ docker build -t <docker-id>/redis:latest . 			# '.' is the build context
```

***

## 36: Manual Image Generation with Docker Commit

```
1. $ docker run -it alpine sh                               # -i: attach our terminal to the STDIN channel of the new running process; # -t: show text in readable form 
2. $ apk add --update redis 				                # Inside the container manually install redis
3. $ docker ps 							                    # Get CONTAINER_ID
4. $ docker commit -c 'CMD ["redis-server"]' CONTAINER_ID 	# CONTAINER_ID ??
5. $ docker image ls -a
6. $ docker run IMAGE_ID
```

***
