## 89: Services Overview

```
$ npm run start # Starts up a development server.
$ npm run test # Runs tests associated with the project.
$ npm run build # Builds a production version of the application.
```

* Github / Travis CI (Run tests automatically) / AWS

## 90: Github Setup

```
$ git remote add origin git@github.com:<user-id>/<repo-name>.git
$ git push origin master
```

## 91: Travis CI Setup

* Your Laptop 
* push to GitHub 
* Travis (Run tests)
* Travis (Automatically deploy the code to AWS)

https://travis-ci.org/ (Sign in with GitHub) --> Authorize travis-ci

## 92: Travis YML File Configuration

* Flow
  - Tell Travis we need a copy of docker running  
  - Build our image using Dockerfile.dev
  - Tell Travis how to run our test suite
  - Tell Travis how to deploy our code to AWS

* Run tests --> If all tests pass --> Deploy to AWS

* Create a file `.travis.yml`           # Notice there is a leading '.'

```
sudo: required # super user level permission
services:
  - docker # Tell Travis we need a copy of docker running

before_install:
  - docker build -t muarshad01/docker-react -f Dockerfile.dev . # Build our image using Dockerfile.dev
```

## 93: Required Travis Updates

## 94: A Touch More Travis Setup

Update `.travis.yml`

```
sudo: required
services:
  - docker

before_install:
  - docker build -t muarshad01/docker-react -f Dockerfile.dev .

script:
  - docker run muarshad01/docker-react npm run test -- --coverage # -- --coverage is for 'npm run test' 
                                                                  # command to exit automatically
```

## 95: Automatic Build Creation

```
$ git add .
$ git commit -m "added travis file"
$ git push origin master
```

## 96: AWS Elastic Beanstalk

## 97: More on Elastic Beanstalk

## 98: Travis Config for Deployment

```
sudo: required
services:
  - docker

before_install:
  - docker build -t muarshad01/docker-react -f Dockerfile.dev .

script:
  - docker run muarshad01/docker-react npm run test -- --coverage # -- --coverage is for 'npm run test' 
                                                                  # command to exit automatically

deploy:
  provider: elasticbeanstalk
  region: "us-west-2"
  app: docker-react
  env: "Docker-env"
  bucket_name: elasticbeanstalk-us-west-2-123456789 # S3 bucket_id
  bucket_path: "docker-react" # same as app name
  on:
    branch: master
```

## 99: Travis Keys Update

```
access_key_id: $AWS_ACCESS_KEY
secret_access_key: $AWS_SECRET_KEY
```

## 100: Automated Deployments

```
sudo: required
services:
  - docker

before_install:
  - docker build -t muarshad01/docker-react -f Dockerfile.dev .

script:
  - docker run muarshad01/docker-react npm run test -- --coverage # -- --coverage is for 'npm run test' 
                                                                  # command to exit automatically

deploy:
  provider: elasticbeanstalk
  region: "us-west-2"
  app: docker-react
  env: "Docker-env"
  bucket_name: elasticbeanstalk-us-west-2-123456789 # S3 bucket_id
  bucket_path: "docker-react" # same as app name
  on:
    branch: master
  access_key_id: $AWS_ACCESS_KEY
  secret_access_key:
    secure: "$AWS_SECRET_KEY"
```

access_key_id: $AWS_ACCESS_KEY

## 101: Exposing Ports Through the Dockerfile

```
FROM node:alpine as builder
WORKDIR '/app'
COPY package.json .
RUN npm before_install
COPY . .
RUN npm run build

FROM nginx
EXPOSE 80 # elasticbeanstalk will look at EXPOSE
COPY --from=builder /app/build /usr/share/nginx/html
```

## 102: AWS Build Still Failing

## 103: Workflod With Github

```
$ git checkout -b feature # switched to a new branch 'feature'

$ git status
$ git add . 
$ git commit -m "updated app.js"
$ git push origin feature # push changes to feature branch

pull/merge branch
```

## 104: Redeploy on Pull Request Merge

## 105: Deployment Wrapup

## 106: Environment Cleanup
