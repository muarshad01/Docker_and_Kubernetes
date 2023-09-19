## 135. Production Multi-Container Deployments

* Push code to `GitHub`
* `Travis` automatically PULLS repo
* `Travis` builds an **test** image, tests code
* `Travis` builds **prod** images
* `Travis` pushed build **prod** images to Docker Hub
* `Travis` pushes project to `AWS EB`
* `EB` pulls images from Docker Hub, deploys

* Services:
  - nginx (Routing Traffic)
  - client (Frontend: React Server)
  - server (Backend: Express Server)
  - worker

***

## 136. Production Dockerfiles

```
$ cd worker
$ touch Dockerfile
```

```
FROM node:alpine
WORKDIR "/app"
COPY ./package.json ./
RUN npm install
COPY . .
CMD ["npm", "run", "start"]             # "dev" is for development mode; "start" is for production mode
```

```
$ cd server
$ touch Dockerfile
```

```
FROM node:14.4.0-alpine
WORKDIR "/app"
COPY ./package.json ./
RUN npm install
COPY . .
CMD ["npm", "run", "start"]             # "dev" is for development mode; "start" is for production mode
```

```
$ cd nginx
$ touch Dockerfile
```

```
FROM nginx
COPY ./default.conf /etc/nginx/conf.d/default.conf
```

***

## 137. Multiple `Nginx` Instances

* `nginx` ROUTING
    - 1. React Server (port 3000)
    - 2. Express Server (port 5000)

***

## 138. Nginx fix for React Router

***

## 139. Altering Nginx's Listen Port

```
$ mkdir nginx
$ cd nginx
$ default.conf
```

```
server {
  listen 3000;

  location / {                          # routing rule
    root /usr/share/nginx/html;
    index index.html index.htm;
  }
}
```

```
$ touch Dockerfile
```

```
FROM node:alpine as buildr
WORKDIR '/app'
COPY ./package.json ./
COPY npm install
COPY . . 
RUN npm run build

FROM nginx
EXPOSE 3000
COPY ./nginx/default.conf /etc/nginx/conf.d/default.conf
COPY --from=builder /app/build /usr/share/nginx/html            # copy from previous build
```

***

## 140. Cleaning Up Tests

***

## 141: Travis Configuration Setup

Create a file `.travis.yml`

```
$ touch .travis.yml
```

```
sudo: required
services:
  - docker

before_install:
  - docker build -t muarshad01/react-test -f ./client/Dockerfile.dev ./client

script:
  - docker run muarshad01/react-test npm test -- --coverage

after_success:
  - docker build -t muarshad01/multi-client ./client
  - docker build -t muarshad01/multi-nginx  ./nginx
  - docker build -t muarshad01/multi-server ./server
  - docker build -t muarshad01/multi-worker ./worker
```

***

## 142. Fix for Failing Travis Builds

```
script:
  - docker run USERNAME/react-test npm test -- --coverage
instead should be:

script:
  - docker run -e CI=true USERNAME/react-test npm test
```

***

## 143. Github and Travis CI Setup

Steps:
  - push code to `github`
  - `Travis` automatically pulls repo
  - `Travis` builds a **test** image, tests code
  - `Travis` build **prod** images
  - `Travis` pushed build prod images to Docker Hub
  - `Travis` pushed project to `AWS EB`
  - EB pulls images from Docker Hub, deploys

```
$ cd complex
$ git init
$ git add .
$ git commit -m "initial commit"
```

Before this, we need to create a repo on `github.com` and then

```
$ git remote add origin git@github.com:muarshad01/<repo-name>.git
$ git remote add origin git@github.com:muarshad01/multi-docker.git
$ git remote -v 
$ git push origin master
```

Next step is to create a link b/w github and Travis-cs

`http://travis-ci.org` --> profile --> `sync account` 

***

## 144. Pushing Images to Docker Hub

```
sudo: required
services:
  - docker

before_install:
  - docker build -t muarshad01/react-test -f ./client/Dockerfile.dev ./client

script:
  - docker run muarshad01/react-test npm test -- --coverage

after_success:
  - docker build -t muarshad01/multi-client ./client
  - docker build -t muarshad01/multi-nginx  ./nginx
  - docker build -t muarshad01/multi-server ./server
  - docker build -t muarshad01/multi-worker ./worker
  # Log in to the docker CLI
  - echo "$DOCKER_PASSWORD" | docker login -u "$DOCKER_ID" --password-stdin
  # Take those images and push them to docker hub
  - docker push muarshad01/multi-client
  - docker push muarshad01/multi-nginx
  - docker push muarshad01/multi-server
  - docker push muarshad01/multi-worker
```

***

## 145. Successful Image Building

***
