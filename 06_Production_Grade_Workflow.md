## 59: Development Workflow

* Author and publish an application using Docker

* Flow: `Development` --> `Testing` --> `Deployment`

***

## 60: Flow Specifics

* GitHub repo with two branches: 
  - `feature`, and 
  - `master`

* `feature-branch` is our development-branch and `master-branch` is a very-clean-copy of our working-code-base.

* **Dev Phase**: We're only pulling/pushing code to the `feature-branch`. 
* We're never going to push code directly to `master-branch`. 

* `Pull Request (PR)` merges the code from `feature-branch` to the `master-branch`.

* **Test-and-PROD Phase**: `Travis CI` service (Continuous Integration Provider) pulls code from `master-branch` and runs TESTS and then merges PR with `master-branch` before automatically pushing-the-code to AWS hosting.

Steps: 
1. Push code to `feature-branch`
2. `Pull Request (PR)` to merge code to `master-branch`
3. `Travis CI` --> Testing & AWS Hosting (Elastic Beanstalk)

***

## 61: Docker's Purpose

* Docker is a **tool** in a normal development flow. 
* Docker makes some of these tasks a lot easier.

***

## 62: Project Generation

Check to see if you've `node.js` installed locally

```
$ node -v
```

* Generate a project and wrap that up within a docker container.

## 63. More on Project Generation

Now that `node.js` is installed. We'll install a little **tool** that creates an `empty-react-project` and then we'll wrap that project inside a container.

```
$ npx create-react-app frontend               # create-react-app inside a new-folder frontend
```

***

## 64: Necessary Commands

How this container will interact with outside world.

```
$ cd ~/frontend

$ npm run start         # Starts up a development server. For development use only
$ npm run test          # Runs tests associated with the project
$ q                     # quit
$ npm run build         # Builds a production version of the application
                        # Creating an optimized production build...
```

```
$ vim ~/frontend/build/index.html
$ vim ~/fontend/build/static/js/main.9ff6fae3.chunk.js
```

```
$ npm run start         # A default react application is launched in your browser `http://localhost:3000/`
```

***

## 65: Creating the `Dev Dockerfile`

```
$ touch `Dockerfile.dev`
```

```
FROM node:16-alpine             # we need a specific version of Node to avoid bugs!

WORKDIR '/app'

COPY package.json .

RUN npm install

COPY . .

CMD ["npm", "run", "start"]
```

```
$ docker build .                        # Error: Dockerfile: no such file or directory
$ docker build -f Dockerfile.dev . 
```

***

## 66: Duplicating Dependencies

delete `node_modules` folder within the frontend project

```
$ cd ~/frontend
$ rm -rf node_modules
``` 

```
$ docker build -f Dockerfile.dev .
```

***

## 67: Starting the Container

```
$ docker run IMAGE_ID
$ docker run -it -p 3000:3000 CONTAINER_ID          # (localhost-port):(container-port)
```

***

## 68: Docker Volumes

* With Docker Volume, we essentially set-up a place-holder of sorts inside our docker container. 
* Instead of copying the local-files, we can create a REFERENCE inside the docker that points to local-files on host-machines. 
* This can be though of as a kind of port- mapping we did earlier, where we map a `port-inside-a-container` to a `port-outside-a-container`. 
* Similarly, here we map a `file-or-directory-inside-a-container` to a `file-or-directory-outside-a-container`.
* Allows us to access files in local-machine

```
$ docker build -f Dockerfile.dev .
$ docker run -p 3000:3000 -v $(pwd):/app IMAGE_ID                   # sh: react-scripts: not found
```

***

## 69: WSL &  Windows Users Must Read Before Next Lecture

***

## 70: Bookmarking Volumes

```
$ docker run -p 3000:3000 -v /app/node_modules -v $(pwd):/app IMAGE-ID
```

***

## 71: Shorthand with `docker-compose`

Create a file `docker-compose.yml`

```
version: '3'
services:                               # All service of containers
	web:
		build: .                        # '.' means Dockerfile
        	ports:
          		- "3000":"3000"         # '-' means a list
        	volumes:
          		- /app/node_modules
          		- .:/app                # Map the current folder-outside-the-container to /app folder-inside-the-container
```

```
$ docker-compose up                     # ERROR: Cannot locate specified Dockerfile: Dockerfile
```

***

## 72: Overriding `Dockerfile` Selection

Update `docker-compose.yml`

```
version: '3'
services:
	web:
    		build:
			context: .                  # where we want file and folders for this image to be pulled from
			dockerfile: Dockerfile.dev
    		ports:
      			- "3000:3000"
    		volumes:
      			- /app/node_modules
      			- .:/app
```

```
$ docker-compose up
```

***

## 73: Do We Need Copy?

***

## 74: Executing Tests

```
$ npm run start                     # Starts up a development server. For development use only
$ npm run test                      # Runs tests associated with the project
$ npm run build                     # Builds a production version of the application

$ docker build -f Dockerfile.dev .

$ docker run IMAGE_ID CMD

$ docker run IMAGE_ID     npm run test
$ docker run CONTAINER_ID npm run test

$ docker run -it CONTAINER_ID npm run test
press Enter
```

***

## 75: Live Updating Tests

Edit the file `App.test.js` and duplicate the test

```
$ docker-compose up
$ docker ps                                         # CONTAINER_ID
$ docker exec -it CONTAINER_ID npm run test
```

***

## 76: Docker Compose for Running Tests

Update `docker-compose.yml` file

```
version: '3'
services:
	web:                                    # Ist service
    	build:
      		context: .
      		dockerfile: Dockerfile.dev
    	ports:
      		- "3000:3000"
    	volumes:
    		- /app/node_modules
    		- .:/app
    tests:                                  # 2nd service
    	build:
        	context: .
        	dockerfile: Dockerfile.dev
    	volumes:
    		- /app/node_modules
    		- .:/app
    	command: ["npm", "run", "test"]
```

```
$ docker-compose up --build
```

***

## 77: Shortcomings on Testing

```
$ npm run test                              # start a test container
$ npm run start                             # start a web container

$ docker ps                                 # CONTAINER-ID
$ docker exec CONTAINER_ID sh
$ docker attach CONTAINER-ID                # Attached our terminal (cmd line) to stdin, stdout, stderr of primary process
$ docker exec -it CONTAINER-ID sh           # attach terminal to STDIN of primary process of container
```

***

## 78: Need for `nginx` (WebServer)

* `nginx`: Takes incoming traffic and
  - routes it and 
  - responds with static files.

***

## 79: Multi-Step Docker Builds

* Two different BSE images
* `Dockerfile` with multi-step build process
* Two different blocks of configuration
* From RUN-Phase we'll reach to BUILD-Phase and copy over to RUN-Phase

* **1. BUILD-Phase**
  - Use `node:alpine`    -- `FROM node:alpine`
  - `package.json` file  -- `COPY package.json .`
  - Install dependencies -- `RUN npm install`
  - Run `npm run build`  -- `CMD ["npm", "run", "build"]`

* **2. RUN-Phase**
  - Use `nginx` -- `FROM nginx`
  - Copy over the result of `npm run build`
  - Start `nginx`

***

## 80: Implementing Multi-Step Builds

Create `Dockerfile`

```
FROM node:16-alpine as builder             # BUILD-Phase: Tag the phase/stage as 'builder'

WORKDIR '/app'

COPY package.json .

RUN npm install                         # Dependencies only needed to execute `npm run build`

COPY . .

RUN npm run build

#---

FROM nginx                              # RUN-Phase

COPY --from=builder /app/build /user/share/nginx/html           # COPY --from=<phase> <src> <dest:nginx-folder-for-static-content>
```

***

## 81: Running `nginx`

```
$ docker build .
$ docker run -p 8080:80 CONTAINER_ID        # (local-machine-port):(container-port)
```
