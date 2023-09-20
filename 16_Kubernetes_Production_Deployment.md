## 256: The Deployment Process

1. Create a Github Repo
2. Tie repo to 'Travis CI Service'
-- Travis CI will be incharge of building our images and pushing them off to DockerHub; 
   To also deploy our application to Kubernetes cluster 
3. Create Google Cloud project
4. Enable billing for the project
5. Add deployment scripts to the repo

***

## 257: Google Cloud vs AWS for Kubernetes

Why Google Cloud?
1. Google created Kubernetes!
2. AWS only "recently" got Kubernetes support
3. Far, far easire to poke around Kubernetes on Google Cloud
4. Excellent documentation for beginners

***

## 258: Creating a Git Repo

$ git remote -v
origin ... (fetch)
origin ... (push)
$ git remote remove origin
$ git remote -v 
$ git remote add origin git@github.com:muarshad01/multi-k8s.git
$ git remote -v
origin ... (fetch)
origin ... (push)
$ git add .
$ git commit -m "added files"
$ git push origin master

***

## 259: Linking the Github Repo to Travis

`https://travis-ci.org`

***

## 260: Free Google Cloud Credits

***

## 261: Creating a Google Cloud Project

`https://console.cloud.google.com`

Select a project --> New Project --> Project Name --> multi-k8s --> Create

***

## 262: Linking a Billing Account

***

## 263: Updated GKE creation steps for new Google Cloud UI

***

## 264: Kubernetes Engine Init

***

## 265: Creating a Cluster with Google Cloud

***

## 266: Don't Forget to Cleanup!

`https://www.udemy.com/docker-and-kubernetes-the-complete-guide/learn/v4/t/lecture/11684242?start=0`

***

## 267: Kubernetes Dashboard on Google Cloud

***

## 268: Travis Deployment Overview

* 1. Install Google Cloud SDK CLI
* 2. Configure the SDK without Googld Cloud auth info
* 3. Login to Docker CLI
* 4. Build the 'test' version of multi-client
* 5. Run tests 
* 6. If tests are successful, run a script to deploy newest images 
* 7. Build all our images, tag each one, push each to docker hub 
* 8. Apply all configs in the 'k8s' folder 
* 9. Imperatively set latest images on each deployment 

***

## 269: Installing the Google Cloud SDK

$ touch .travis.yaml

```
sudo: required 						# Because we're making use of docker
services:
    - docker 						# We need docker to be pre-installed to run our images
before_install:
    - curl https://sdk.cloud.google.com | bash  > /dev/null;
    - source $HOME/google-cloud-sdk/path.bash.inc
    - gcloud components update kubectl
    - gcloud auth activate-service-account --key-file service-account.json
```

***

## 270: Updated Service Account steps for new GCP UI

***

## 271: Generating a Service Account

* Create a Service Account
* Download service account credentials in a <file-name>.json file
* Download and install the Travis CLI
* Encrypt and upload the json file to our Travis account
* In travia.yml, add code to unencrypt the json file and load it into GCloud SDK

***

## 272: Ruby Version Fix

```
$ docker run -it -v $(pwd):/app ruby:2.4 sh
$ gem install travis
```

***

## 273: Running Travis CLI in a Container

`https://github.com/travis-ci/travis.rb`

Installing Ruby is a pain!
Let's get a Docker image that has Ruby pre-installed, then we can install travis CLI in there!

```
$ docker run -it -v $(pwd):/app ruby:2.3 sh 				# '-v' volume
$ gem install travis --no-rdoc --no-ri  					# Don't install documentation
$ gem install travis
$ travis login 
$ travis login --github-token GIT_HUB_TOKEN --com
```

Copy json file into the 'volume' directory so we can use it in the container 
```
$ travis encrypt-file <file-name> -r muarshad01/multi-k8s  --com 			# '-r' repository 
$ travis encrypt-file service-account.json -r muarshad01/multi-k8s  --com
```

***

## 274: Travis Login Issues, "iv undefined" or "repository not known"

***

## 275: Encrypting a Service Account File

```
# travis login

Username: muarshad01
Pasword: Github-password

$ travis encrypt-file <file-name> -r muarshad01/multi-k8s 			# '-r' repository 
$ travis encrypt-file service-account.json -r muarshad01/multi-k8s --com

service-account.json.enc
$ rm -f service-account.json 
# exit 
```

***

## 276: More Google Cloud CLI Config

```
sudo: required 								# Because we're making use of docker
services:
    - docker 								# We need docker to be pre-installed to run our images
before_install:
    - curl https://sdk.cloud.google.com | bash  > /dev/null;
    - source $HOME/google-cloud-sdk/path.bash.inc
    - gcloud components update kubectl
    - gcloud auth activate-service-account --key-file service-account.json
    - gcloud config set project skilful-berm-214822
    - gcloud config set compute/zone us-central1-a 
    - gcloud container clusters get-credentials multi-cluster  
    - echo "$DOCKER_PASSWORD" | docker login -u "$DOCKER_USERNAME" --password-stdin
    - docker build -t muarshad01/react-test -f ./client/Dockerfile.dev ./client

script:
    - docker run CI=true muarshad01/react-test npm test
```

***

## 277: Fix For Failing Travis Builds

***

## 278: Running Testes with Travis

***

## 279: Custom Deployment Providers

In .travis.yaml file add the following:

```
deploy:
    provider: script 
    script: bash ./deploy.sh 
    on:
        branch: master
```

$ touch deploy.sh 

***

## 280: Unique Deployment Images

deploy.sh

```
$ docker build -t muarshad01/multi-client -f ./client/Dockerfile ./client
$ docker build -t muarshad01/multi-server -f ./server/Dockerfile ./server 
$ docker build -t muarshad01/mulit-worker -f ./worker/Dockerfile ./worker 

$ docker push muarshad01/multi-client
$ docker push muarshad01/multi-server 
$ docker push muarshad01/multi-worker

$ kubectl apply -f k8s 
$ kubectl set image deployments/server-deployment server=muarshad01/multi-server 
```

***

## 281: Unique Tags for Built Images

```
$ docker buid 
-t docker_id/multi-client:latest
-t docker_id/multi-client:$GIT_SHA
-f ./client/Dockerfile ./client
```

***

## 282: Updating the Deployment Script

In .travis.yaml

```
services:
    - docker
env:
    global:
        - SHA=$(git rev-parse HEAD) # get the latest revision or commit id
        - CLOUDSDK_CORE_DISABLE_PROMPTS=1
```

***

## 283: Configuring the GCloud CLI on Cloud Console

```
$ gcloud config set project <ID>
$ gcloud config set compute/zone us-central-account
$ gcloud container clusters get-credentials multi-cluster 
```

***

## 284: Creating a Secret on Google Cloud

```
$ kubectl get pods 

$ kubectl create secret generic <secret_name> --from-literal PGPASSWORD=password123
$ kubectl create secret generic pgpassword --from-literal PGPASSWORD=mypgpassword123 
```

***

## 285: Helm v3 Update

***

## 286: Helm Setup

```
$ github.com/helm/helm 
```

Helm Client --> Tiller Server           # runs inside a kubernetes server; Tiller is a pod 

***

## 287: Kubernetes Security with RBAC

* RBAC
  - Limits who can access and modify objects in our cluster
  - Enabled on Google Cloud by default
  - Tiller wants to make changes to our cluater, so it needs to get some permissions set

```
$ kubectl get namespces 
```

***

## 288: Assigning Tiller a Service Account

```
$ kubectl create serviceaccount --namespace kube-system tiller 
$ kubectl create clusterrolebinding tiller-cluster-rule --clusterrole=cluster-admin --serviceaccount=kube-system:tiller
```

$ helm --service-account tiller --upgrade

***

## 289: Ingress-Nginx with Helm

$ helm install stable/nginx-ingress --name my-nginx --set rbac.create=true

***

## 291: The Result of Ingress-Nginx

***

## 292: Finally - Deployment

`https://github.com/StephenGrider/DockerCasts`

***

## 293: Did I really Type That?

***

## 294: Verifying Deployment

***

## 295: A Workflow for Chaining in Prod

* Check out a branch 
* Make changes 
* Commit changes 
* Push to github branch 
* Create a PR (Pull Request)
* Wait for tests to show up 'green' 
* Merge the PR (Pull Request)
* See changes appear on prod 

```
$ git checkout branch -b devel 
$ git status 
$ git add .
$ git commit -m "updated header" 
$ git push origin devel 
```

Create a pull-request manually 

***

## 296: Merging a PR (Pull Request) for Deployment

***

## 297: That's It! What's Next?

***

## 298: Completed Code For Google Cloud Deployment
