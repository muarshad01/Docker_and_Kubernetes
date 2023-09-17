## 82: Services Overview

```
$ npm run start             # Starts up a development server.
$ npm run test              # Runs tests associated with the project.
$ npm run build             # Builds a production version of the application.
```

* `GitHub` -> `Travis CI` (Run tests automatically) -> `AWS`

***

## 83: GitHub Setup

```
$ git remote add origin git@github.com:<user-id>/<repo-name>.git
$ git push origin master
```

***

## 84: Travis and Account Registration

***

## 85: Travis CI Setup

* 1. Your Laptop -> 2. push to `GitHub` -> 3. `Travis` (Run tests) -> 4. Travis (Automatically deploy the code to `AWS`)

* `https://travis-ci.org/` -> (Sign in with GitHub) --> Authorize `travis-ci`

***

## 86: `Travis YML` File Configuration

* Flow
  - Tell Travis, we need a copy of `docker` running  
  - Build our image using `Dockerfile.dev`
  - Tell Travis, how to run our test-suite?
  - Tell Travis, how to deploy our-code-to-AWS?

* Run tests -> If all tests PASS -> Deploy to AWS

* Create a file `.travis.yml`           # Notice there is a leading '.'

```
sudo: required              # superuser-level permission
services:
  - docker                  # Tell Travis, we need a copy of `docker` running

before_install:
  - docker build -t muarshad01/docker-react -f Dockerfile.dev .             # Build our image using `Dockerfile.dev`
```

***

## 87: A Touch More Travis Setup

Update `.travis.yml`

```
sudo: required
services:
  - docker

before_install:
  - docker build -t muarshad01/docker-react -f Dockerfile.dev .

script:
  - docker run -e CI=true muarshad01/docker-react npm run test              # updated script
```

```
script OLD:
#  - docker run muarshad01/docker-react npm run test -- --coverage      # -- --coverage is for 'npm run test' 
                                                                        # command to exit automatically
```

***

## 88: Automatic Build Creation

```
$ git add .
$ git commit -m "added travis file"
$ git push origin master
```

***

## 89: Required `AWS Elastic Beanstalk` Environment Setup Updates

***

## 90: `AWS Elastic Beanstalk`

***

## 91: More on `Elastic Beanstalk`

***

## 92: Travis Config for Deployment

```
sudo: required
services:
  - docker

before_install:
  - docker build -t muarshad01/docker-react -f Dockerfile.dev .

script:
  - docker run muarshad01/docker-react npm run test -- --coverage               # -- --coverage is for 'npm run test' 
                                                                                # command to exit automatically

deploy:
  provider: elasticbeanstalk
  region: "us-west-2"
  app: docker-react
  env: "Docker-env"
  bucket_name: elasticbeanstalk-us-west-2-123456789                 # S3 bucket_id
  bucket_path: "docker-react"                                       # same as app name
  on:
    branch: master
```

***

## 93: Required Update for `IAM` User and Keys

```
access_key_id: $AWS_ACCESS_KEY
secret_access_key: $AWS_SECRET_KEY
```

***

## 94: Automated Deployments

```
sudo: required
services:
  - docker

before_install:
  - docker build -t muarshad01/docker-react -f Dockerfile.dev .

script:
  - docker run muarshad01/docker-react npm run test -- --coverage           # -- --coverage is for 'npm run test' 
                                                                            # command to exit automatically

deploy:
  provider: elasticbeanstalk
  region: "us-west-2"
  app: docker-react
  env: "Docker-env"
  bucket_name: elasticbeanstalk-us-west-2-123456789                 # S3 bucket_id
  bucket_path: "docker-react"                                       # same as app name
  on:
    branch: master
  access_key_id: $AWS_ACCESS_KEY
  secret_access_key:
    secure: "$AWS_SECRET_KEY"
```

***

## 95: Exposing Ports Through the `Dockerfile`

```
FROM node:alpine as builder
WORKDIR '/app'
COPY package.json .
RUN npm before_install
COPY . .
RUN npm run build

#---

FROM nginx
EXPOSE 80                                                   # elasticbeanstalk will look at EXPOSE
COPY --from=builder /app/build /usr/share/nginx/html        # COPY --from=<phase> <src> <dst-on-ngix>
```
***

## 96: Workflow With GitHub

```
$ git checkout -b feature                       # switched to a new branch 'feature'

$ git status
$ git add . 
$ git commit -m "updated app.js"
$ git push origin feature                       # push changes to feature branch

```

PR/merge branch

***

## 97: Redeploy on `Pull Request (PR) Merge`

***

## 98: Deployment Wrapup

***

## 99: Environment Cleanup

***

## 100: AWS Configuration Cheat-Sheet

***

## 101: Finished Project Code with Updates Applied

***
