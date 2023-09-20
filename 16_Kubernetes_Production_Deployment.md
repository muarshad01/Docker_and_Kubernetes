## 250: The Deployment Process

* Create a `GitHub` Repo
* Tie Repo to `Travis CI` Service
    - `Travis CI` will be in-charge of building our images and pushing them off to `DockerHub`; 
    - To also deploy our application to `Kubernetes` cluster 
* Create Google Cloud project
* Enable billing for the project
* Add deployment scripts to the Repo

***

## 251: `Google Cloud` vs `AWS` for Kubernetes

* Why Google Cloud?
    - Google created `Kubernetes`
    - `AWS` only "recently" got `Kubernetes` support
    - Far, far easier to poke around `Kubernetes` on Google Cloud
    - Excellent documentation for beginners

***

## 252: Creating a Git Repo

```
$ git remote -v
-- origin ... (fetch)
-- origin ... (push)
```

```
$ git remote remove origin
$ git remote -v 
```

```
$ git remote add origin git@github.com:muarshad01/multi-k8s.git
```

```
$ git remote -v
-- origin ... (fetch)
-- origin ... (push)
```

```
$ git add .
$ git commit -m "added files"
$ git push origin master
```

***

## 253: Linking the `GitHub` Repo to `Travis`

`https://travis-ci.org`

***

## 254: Free Google Cloud Credits

***

## 255: Creating a Google Cloud Project

`https://console.cloud.google.com`

* Select a project -> New Project -> Project Name -> multi-k8s --> Create

***

## 256: Linking a Billing Account

***

## 257: Updated GKE Creation Steps for new Google Cloud UI

***

## 258: `Kubernetes` Engine Init

***

## 259: Creating a Cluster with Google Cloud

***

## 260: Don't Forget to Cleanup!

`https://www.udemy.com/docker-and-kubernetes-the-complete-guide/learn/v4/t/lecture/11684242?start=0`

***

## 261: `Kubernetes` Dashboard on Google Cloud

***

## 262: `Travis` Deployment Overview

* 1. Install Google Cloud SDK CLI
* 2. Configure the SDK without Googld Cloud `Auth Info`
* 3. Login to Docker CLI
* 4. Build the `Test-version` of multi-client
* 5. Run Tests 
* 6. If Tests are SUCCESSFUL, run a script to deploy newest images 
* 7. Build all our images, `Tag` each one, PUSH each to docker-hub 
* 8. Apply all configs in the `k8s` folder 
* 9. Imperatively set latest images on each deployment 

***

## 263: Installing the Google Cloud SDK

Create `.travis.yaml`

```
sudo: required
services:
    - docker 						# We need docker to be pre-installed to run our images
before_install:
    - curl https://sdk.cloud.google.com | bash  > /dev/null;
    - source $HOME/google-cloud-sdk/path.bash.inc
    - gcloud components update kubectl
    - gcloud auth activate-service-account --key-file service-account.json
```

***

## 264: Updated Service Account steps for new `GCP UI`

***

## 265: Generating a `Service Account`

* Create a `Service Account`
* Download `service-account-credentials` in a `<file-name>.json` file
* Download and install the `Travis CLI`
* Encrypt and upload the `json` file to our `Travis` account
* In `travia.yaml`, add code to un-encrypt the json file and load it into `GCloud SDK`

***

## 266: Ruby Version Fix

```
$ docker run -it -v $(pwd):/app ruby:2.4 sh
$ gem install travis
```

***

## 267: Running `Travis CLI` in a Container

`https://github.com/travis-ci/travis.rb`

* Installing Ruby is a pain!
* Let's get a Docker image that has Ruby pre-installed, then we can install Travis CLI in there!

```
$ docker run -it -v $(pwd):/app ruby:2.3 sh 				# '-v' volume
$ gem install travis --no-rdoc --no-ri  					# Don't install documentation
$ gem install travis
$ travis login 
$ travis login --github-token GIT_HUB_TOKEN --com
```

*Copy json file into the `volume` directory, so that we can use it in the container 

```
$ travis encrypt-file <file-name> -r muarshad01/multi-k8s  --com 			# '-r' repository 
$ travis encrypt-file service-account.json -r muarshad01/multi-k8s  --com
```

***

## 268: Travis Login Issues, "iv undefined" or "repository not known"

***

## 269: Encrypting a Service Account File

```
# travis login

-- Username: muarshad01
-- Pasword: Github-password
```

```
$ travis encrypt-file <file-name> -r muarshad01/multi-k8s 			# '-r' repository 
$ travis encrypt-file service-account.json -r muarshad01/multi-k8s --com
```

`service-account.json.enc`

```
$ rm -f service-account.json 
# exit 
```

***

## 270: More Google Cloud CLI Config

```
sudo: required
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

## 271: Fix For Failing Travis Builds

***

## 272: Running Testes with Travis

***

## 273: Custom Deployment Providers

In `.travis.yaml` file add the following:

```
deploy:
    provider: script 
    script: bash ./deploy.sh 
    on:
        branch: master
```

Create `deploy.sh` 

***

## 274: Unique Deployment Images

File `deploy.sh`

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

## 275: Unique Tags for Built Images

```
$ docker build -t <docker_id>/multi-client:latest -t <docker_id>/multi-client:$GIT_SHA -f ./client/Dockerfile ./client
```

***

## 276: Updating the Deployment Script

File `.travis.yaml`

```
services:
    - docker
env:
    global:
        - SHA=$(git rev-parse HEAD)                 # get the latest revision or commit id
        - CLOUDSDK_CORE_DISABLE_PROMPTS=1
```

***

## 277: Configuring the GCloud CLI on Cloud Console

```
$ gcloud config set project <ID>
$ gcloud config set compute/zone us-central-account
$ gcloud container clusters get-credentials multi-cluster 
```

***

## 278: Creating a Secret on Google Cloud

```
$ kubectl get pods 
```

```
$ kubectl create secret generic <secret_name> --from-literal PGPASSWORD=password123
$ kubectl create secret generic pgpassword    --from-literal PGPASSWORD=mypgpassword123 
```

***

## 279: Helm v3 Update

***

## 280: Helm Setup

`https://github.com/helm/helm`

* `Helm Client` -> `Tiller Server`           # runs inside a kubernetes server; Tiller is a pod 

***

## 281: `Kubernetes` Security with `RBAC`

* RBAC
    - Limits who can access-and-modify objects in our cluster
    - Enabled on Google Cloud by default
    - `Tiller` wants to make changes to our cluster, so it needs to get some `permissions-set`

```
$ kubectl get namespaces 
```

***

## 282: Assigning `Tiller` a Service Account

```
$ kubectl create serviceaccount --namespace kube-system tiller 
$ kubectl create clusterrolebinding tiller-cluster-rule --clusterrole=cluster-admin --serviceaccount=kube-system:tiller
```

```
$ helm --service-account tiller --upgrade
```

***

## 283: `Ingress-Nginx` with `Helm`

```
$ helm install stable/nginx-ingress --name my-nginx --set rbac.create=true
```

***

## 285: The Result of `Ingress-Nginx`

***

## 286: Finally - Deployment

`https://github.com/StephenGrider/DockerCasts`

***

## 287: Did I really Type That?

***

## 288: Verifying Deployment

***

## 289: A Workflow for Chaining in Prod

* checkout a branch (`$ git checkout -b <new-test-branch>`) 
* Make changes 
* Commit changes (`$ git commit -m "updates"`)
* Push to github branch (`$ git push origin <new-test-branch>`) 
* Create a `Pull Request (PR)`
* Wait for tests to show up `GREEN` 
* Merge the PR
* See changes appear on prod 

```
$ git checkout branch -b devel 
$ git status 
$ git add .
$ git commit -m "updated header" 
$ git push origin devel 
```

* Create a PR manually 

***

## 290: Merging a `Pull Request(PR)` for Deployment

***

## 291: That's It! What's Next?

***

## 292: Completed Code For Google Cloud Deployment
