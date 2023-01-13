## 139. Production Multi-Container Deployments

* Push code to github
* Travis automatically pulls repo
* Trais builds an image, test code
* Travis pushed code to AWS EB
* EB builds image, deploys it

* Services:
  - client
  - nginx
  - server
  - worker

## 140. Production Dockerfiles

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
CMD ["npm", "run", "start"] # "dev" is for devlopment mode; "start" is for production mode
```

```
$ cd server
$ touch Dockerfile
```

```
FROM node:alpine
WORKDIR "/app"
COPY ./package.json ./
RUN npm install
COPY . .
CMD ["npm", "run", "start"] # "dev" is for devlopment mode; "start" is for production mode
```

```
$ cd nginx
$ touch Dockerfile
```

```
FROM nginx
COPY ./default.conf /etc/nginx/conf.d/default.conf
```

## 141. Multiple Nginx Instances

* One nginx server a routing job
* Another ngix serving the react

## 142. Altering Nginx's Listen Port

```
$ cd client
$ mkdir nginx
$ cd nginx
$ default.conf
```

```
server {
  listen 3000;

  location / { # routing rule
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
COPY --from=builder /app/build /usr/share/nginx/html # copy from previous build
```

## 143: Nginx fix for react Router

## 144. Cleaning Up Tests

## 145. Github and Travis CI Setup

Steps:
  push code to github
  Travis automatically pulls repo
  Travis builds a test image, tests code
  Travis build prod images
  Travis pushed build prod images to Docker Hub
  Travis pushed project to AWS EB
  EB pulls images from Docker Hub, deploys

```
$ cd complex
$ git init
$ git add .
$ git commit -m "initial commit"
```

Before this, we need to create a repo on github.com and then

```
$ git remote add origin git@github.com:muarshad01/<repo-name>.git
$ git remote add origin git@github.com/muarshad01/multi-docker.git
$ git remote -v 
$ git push origin master
```

Next step is to create a link b/w github and Travis-cs

http://travis-ci.org --> profile --> sync account --> 

## 146. Fix for Failing Travis Builds

script:
  - docker run USERNAME/react-test npm test -- --coverage

instead should be:

script:
  - docker run -e CI=true USERNAME/react-test npm test

## 147: Travis Configuration Setup

creat file .travis.yml

```
$ touch .travis.yml
```

```
sudo: required
services:
  - docker

before_install:
  - docker build -t marshad1/react-test -f ./client/Dockerfile.dev ./client

script:
  - docker run marshad1/react-test npm test -- --coverage

after_success:
  - docker build -t marshad1/multi-client ./client
  - docker build -t marshad1/multi-nginx ./nginx
  - docker build -t marshad1/multi-server ./server
  - docker build -t marshad1/multi-worker ./worker
```

## 148. Pushing Images to Docker Hub

```
sudo: required
services:
  - docker

before_install:
  - docker build -t stephengrider/react-test -f ./client/Dockerfile.dev ./client

script:
  - docker run marshad/react-test npm test -- --coverage

after_success:
  - docker build -t marshad/multi-client ./client
  - docker build -t marshad/multi-nginx ./nginx
  - docker build -t marshad/multi-server ./server
  - docker build -t marshad/multi-worker ./worker
  
  # push to docker hub
  # Log in to the docker CLI
  $ docker login
    - Username:
    - Password:

  Travis --> marshad1/multi-docker project --> More options
                                  --> settings --> Environment Variables
                                  --> DOCKER_ID
                                  --> DOCKER_PASSWORD

  $ echo "$DOCKER_PASSWORD" | docker login -u "$DOCKER_ID" --password-stdin

  # Take those images and push them to docker hub
  - docker push marshad1/multi-client
  - docker push marshad1/multi-nginx
  - docker push marshad1/multi-server
  - docker push marshad1/multi-worker
```

## 149. Successful Image Building
