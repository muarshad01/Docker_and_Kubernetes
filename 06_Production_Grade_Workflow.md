## 61: Development Workflow

* Author and publish an application using Docker

* Flow: `Development` --> `Testing` --> `Deployment`

***

## 62: Flow Specifics

* GitHub repo with two branches: 
  - `feature`, and 
  - `master`

`feature` branch is our development-branch and `master` branch is a very-clean-copy of our working-code-base.

* We're only pulling/pushing code to the `feature` branch. We're never going to push code directly to `master`. `Pull Request` merges the code to the master branch.

* `Travis CI` service pulls code from `master` branch and runs tests and then automatically pushes the code to AWS hosting.

Steps: 
1. Push code to `feature` branch 
2. `Pull Request (PR)` to merge code to `master` branch
3. Travis CI --> AWS Hosting (Elastic Beanstalk)

***

## 63: Docker's Purpose

* Docker is a took in a normal development flow. Docker makes some of these tasks a lot easier.

***

## 64: Project Generation

* Generate a project and wrap that up within a docker container.
* React application/Recat front-end/React framework

```
$ node -v
```

***

## 65: Create-React-App Generation

```
$ npm install -g create-react-app
```

***

## 66: More on Project Generation

Now that `node.js` is installed. We'll install a little tool that crwates an `empty-react-project` and then we'll 
wrap that project inside a container.

```
$ npm install -g create-react-app
$ create-react-app frontend # create a react project named frontend
```

***

## 67: Necessary Commands

How this container will interact with outside world.

```
$ npm run start         # Starts up a development server. For development use only
$ npm run test          # Runs tests associated with the project
$ npm run build         # Builds a production version of the application

$ npm run test
$ q                     # quit

$ npm run build
# Creating an optimized production build...
```

~/frontend/build/index.html
~/fontend/build/static/js/main.9ff6fae3.chunk.js

```
$ npm run start         # A default react application is launched in your browser `http://localhost:3000/`
```

***

## 68: Creating the `Dev Dockerfile`

Create `Dockerfile.dev`

```
FROM node:alpine

WORKDIR '/app'

COPY package.json .

RUN npm install

COPY . .

CMD ["npm", "run", "start"]
```

```
$ docker build .                    # Error: Dockerfile: no such file or directory
$ docker build -f Dockerfile.dev . 
```

***

## 69: Duplicating Dependencies

delete node_modules folder within the frontend project 

```
$ docker build -f Dockerfile.dev .
```

***

## 70: ReactApp Exits Immediately with Docker Run Command

***

## 71: Starting the Container

```
$ docker run IMAGE_ID
$ docker run -it -p 3000:3000 CONTAINER_ID          # (localhost-port):(container-port)
```

***

## 72: Docker Volumes

* With Docker Volume, we essentially set-up a place-holder of sorts inside our docker container. 
* Instead of copying the local-files, we can create a reference inside the docker that points to local-files on host-machines. 
* This can be though of as a kind of port- mapping we did earlier, where we map a port-inside-a-container to a port-outside-a-container. 
* Similarly, here we map a file/directory inside a container to a file or directory outside a container.
* Allows us to access files in local machine

```
$ docker build -f Dockerfile.dev .
$ docker run -p 3000:3000 -v $(pwd):/app image-id       # sh: react-scripts: not found
```

***

## 73: Windows not Detecting Changes - Update

***

## 74: Bookmarking Volumes

```
$ docker run -p 3000:3000 -v /app/node_modules -v $(pwd):/app IMAGE-ID
```

***

## 75: ReactApp Exited With Code 0

***

## 76: Shorthand with Docker Compose

Create a file `docker-compose.yml`

```
version: '3'
services:                               # All service of containers
	web:
		build: .                        # '.' means dockerfile
        	ports:
          		- "3000":"3000"         # '-' means a list
        	volumes:
          		- /app/node_modules
          		- .:/app                # map the current folder outside the container to /app folder inside the container
```

```
$ docker-compose up                     # ERROR: Cannot locate specified Dockerfile: Dockerfile
```

***

## 77: Overriding `Dockerfile` Selection

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

## 78: Windows not Detecting Changes - Docker Compose

***

## 79: Do We Need Copy?

***

## 80: Executing Tests

```
$ npm run start                     # Starts up a development server. For development use only
$ npm run test                      # Runs tests associated with the project
$ npm run build                     # Builds a production version of the application

$ docker build -f Dockerfile.dev .
$ docker run CMD ["npm", "run", "start"]
$ docker run npm CONTAINER_ID npm run test
$ docker run -it CONTAINER_ID npm run test
press Enter
```

***

## 81: Live Updating Tests

Edit the file `App.test.js` and duplicate the test

```
$ docker-compose up
$ docker ps                                     # container-id
$ docker exec -it CONTAINER-ID npm run test
```

***

## 82: Docker Compose for Running Tests

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

## 83. Tests Not Re-running on Windows

***

## 84: Shortcomings on Testing

```
$ npm run test                              # start a test container
$ npm run start                             # start a web container

$ docker ps                                 # CONTAINER-ID
$ docker exec CONTAINER_ID sh
$ docker attach CONTAINER-ID                # Attached our terminal (cmd line) to stdin, stdout, stderr of primary process
$ docker exec -it CONTAINER-ID sh           # attach terminal to STDIN of primary process of container
```

***

## 85: Need for `nginx` (WebServer)

* `nginx`: Takes incoming traffic and
  - routes it and 
  - responds with static files.

***

## 86: Multi-Step Docker Builds

* Build Phase
  - Use node:alpine
  - Copy the `package.json` file
  - Install dependencies
  - Run `npm run build`

* Run Phase
  - Use `nginx`
  - Copy over the result of `npm run build`
  - Start `nginx`

***

## 87: Implementing Multi-Step Builds

Create `Dockerfile`

```
FROM node:alpine as builder             # tag the phase/stage as 'builder'

WORKDIR '/app'

COPY package.json .

RUN npm install                         # Deps only needed to execute 'npm run build'!

COPY . .

RUN npm run build

FROM nginx                              # 2nd phase

COPY --from=builder /app/build /user/share/nginx/html # /app/build <-- all the stuff
```

***

## 88: Running `nginx`

```
$ docker build .
$ docker run -p 8080:80 CONTAINER_ID        # (local-machine-port):(container-port)
```
