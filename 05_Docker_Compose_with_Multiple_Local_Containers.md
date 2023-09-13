## 47: App Overview

* A WebApp that simply displays the number of times this page (`https://localhost:4001`) has been visited.

* `https://localhost:4001` --> Docker Container - `NodeApp` --> Docker Container - `RedisServer`

***

## 48: App Server Starter Code

```
$ mkdir visits
$ cd visits
```

Create file `package.json`:
```
{
    "dependencies": {
        "express": "*",
        "redis": "2.8.0"
    },
    "scripts": {
        "start": "node index.js"
    }
}
```

Create file `index.js`
```
const express = require('express');
const redis = require('redis');

const app = express();
const client = redis.createClient();
client.set('visits', 0);

app.get('/', (req, res) => {
	client.get('visites', (err, visits) => {
		res.send('Number of visits is ' + visits);
		client.set('visits', parseInt(visits) + 1);
	});
});

app.listen(8081, ()=> {
	console.log('Listening on port 8081');
});
```

***

## 49: Assembling a `Dockerfile`

```
$ touch Dockerfile
```

```
FROM node:14-alpine

WORKDIR '/app'

COPY ./package.json .

RUN npm install

COPY . .

CMD ["npm", "start"]
```

```
$ docker build .
```

* You will see the following and this is totally fine: 
  - npm notice created a lockfile as `package-lock.json`. You should commit this file.
  - npm WARN app No description
  - npm WARN app No repository field.
  - npm WARN app No license field.

```
$ docker build -t marshad1/visits:latest . 			# Tag the image <docker-id>/<project-name>:latest
$ docker image ls -a
```

***

## 50: Introducing Docker Compose

```
$ docker run marshad1/visits 				# Error: Redis connection to 127.0.0.1:6379 failed 
                							# - connect ECONNREFUSED 127.0.0.1:6379
$ docker run redis 				        	# Ready to accept connections
```

***

## 51: Docker Compose Files

* Docker Container`NodeApp` <--> `Redis` don't have a COMMUNICATION-CHANNEL between them.

* We need a **networking infrastructure* and there are two options: 
  - 1) Docker CLI's Network Features
  - 2) Docker Compose

* What is Docker Compose?
  - A tool that gets installed along with Docker
  - Separate CLI
  - Used to start-up multiple Docker containers at the same time and CONNECT them in an AUTOMATED way
  - Automates some of the long-winded arguments we were passing to 'docker run'


```
$ docker-compose
```

## 52: Networking with `Docker-Compose`

```
$ docker build -t marshad1/visits:latest .
$ docker run -p 8080:8080 marshad1/visits
```

Create a file `docker-compose.yml`

```
version: '3' 						# Version of docker-compose
services:
	redis-server: 					# Container redis
    		image: 'redis'			# -- Make it use the 'redis' image
  	node-app: 					    # Container node-app
    		build: . 				# -- Make it use the `Dockerfile` in current directory
    		ports: 					# -- Map port 4001 to 8081
      		- "4001:8081" 			# '-' in a yml file is used to specify an arry; or one port here 
```

***


## 53: `Docker-Compose` Commands

Update `index.js`

```
const express = require('express');
const redis = require('redis');

const app = express();
const client = redis.createClient({
	host: 'redis-server',
	port: 6379
});
client.set('visits', 0);

app.get('/', (req, res) => {
    client.get('visits', (err, visits) => {
        res.send('Number of visits is ' + visits);
        client.set('visits', parseInt(visits) + 1);
    });
});

app.listen(8081, () => {
    console.log('Listening on port 8081');
});
```

***


```
$ docker run myimage                  <==> docker-compose up
$ docker build . & docker run myimage <==> docker-compose up --build

$ docker image ls -a | grep '^<none>' | awk '{print $3}' | xargs docker rmi

$ docker-compose up 							# Creating network "visits_default" with default driver. 
		    							# ...
                    							# Creating visits_node-app_1			...done
		    							# Creating visits_redis_node-app_1		...done
		    							# We should see redis-server-1 ...'Ready to accept connection' 
									# and node-app-1 ...Listening on port 8081

$ docker-compose up --build
```

***

## 54: Stopping `Docker-Compose` Containers

```
$ docker run -d redis
$ docker ps
$ docker stop CONTAINER_ID
```

```
$ docker-compose up -d 				# Launch in background
$ docker-compose down
```

***

## 55: Container Maintenance with Compose

```
$ docker-compose up --build 			# 'Rebuild' the container
```

***

## 56: Automatic Container Restarts

* Four types of Restart Policies:
  - "no"           -- Never attempt to restart this container if it stops or crashes
  - always         -- If this container stops 'for any reason' always attempt to restart it
  - on-failure     -- Only restart if the container stops with an error code
  - unless-stopped -- Always restart unless we (the developers) forcibly stop it

***

## 57: Container status with Docker Compose

```
$ dockr run redis
$ docker ps
```

```
$ docker-compose up
$ docker-compose ps 				# Note: Run this command from the folder containing docker-compose.yml
```

***
