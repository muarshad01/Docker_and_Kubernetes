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

***

## 88: Automatic Build Creation

```
$ git add .
$ git commit -m "added travis file"
$ git push origin master
```

***

## 89: Required `AWS Elastic Beanstalk` Environment Setup Updates

```

AWS Configuration Cheat Sheet
-----------------------------
This lecture note is not intended to be a replacement for the videos, but to serve as a cheat sheet for students who want to quickly run thru the AWS configuration steps or easily see if they missed a step. It will also help navigate through the changes to the AWS UI since the course was recorded.

Docker Compose config Update

Make sure to follow the steps in the earlier lecture note to rename your development docker compose file and create a new production compose file:

https://www.udemy.com/course/docker-and-kubernetes-the-complete-guide/learn/lecture/27975358



Create EC2 IAM Instance Profile
-------------------------------
1. Go to AWS Management Console
2. Search for IAM and click the IAM Service.
3. Click Roles under Access Management in the left sidebar.
4. Click the Create role button.
5. Select AWS Service under Trusted entity type. Then select EC2 under common use cases.
6. Search for AWSElasticBeanstalk and select the AWSElasticBeanstalkWebTier, AWSElasticBeanstalkWorkerTier and AWSElasticBeanstalkMulticontainerDocker policies. Click the Next button.
7. Give the role the name of aws-elasticbeanstalk-ec2-role
8. Click the Create role button.



Create Elastic Beanstalk Environment
------------------------------------
1. Go to AWS Management Console
2. Search for Elastic Beanstalk and click the Elastic Beanstalk service.
3. If you've never used Elastic Beanstalk before you will see a splash page. Click the Create Application button. If you have created Elastic Beanstalk environments and applications before, you will be taken directly to the Elastic Beanstalk dashboard. In this case, click the Create environment button. There is now a flow of 6 steps that you will be taken through.
5. You will need to provide an Application name, which will auto-populate an Environment Name.
6. Scroll down to find the Platform section. You will need to select the Platform of Docker. You then must manually change from Docker running on 64bit Amazon Linux 2023 to Docker running on 64bit Amazon Linux 2.
7. Scroll down to the Presets section and make sure that free tier eligible has been selected:
8. Click the Next button to move to Step #2.
9. You will be taken to a Service Access configuration form.
Select Create and use new service role and name it aws-elasticbeanstalk-service-role. You will then need to set the EC2 instance profile to the aws-elasticbeanstalk-ec2-role created earlier (this will likely be auto-populated for you).
10. Click the Skip to Review button as Steps 3-6 are not applicable.
11. Click the Submit button and wait for your new Elastic Beanstalk application and environment to be created and launch.
12. Click the link below the checkmark under Domain. This should open the application in your browser and display a Congratulations message.



Update Object Ownership of S3 Bucket
------------------------------------
1. Go to AWS Management Console
2. Search for S3 and click the S3 service.
3. Find and click the elasticbeanstalk bucket that was automatically created with your environment.
4. Click Permissions menu tab
5. Find Object Ownership and click Edit
6. Change from ACLs disabled to ACLs enabled. Change Bucket owner Preferred to Object Writer. Check the box acknowledging the warning.
7. Click Save changes.



Add AWS configuration details to .travis.yml file's deploy script
-----------------------------------------------------------------
1. Set the region. The region code can be found by clicking the region in the toolbar next to your username. 

eg: 'us-east-1'

2. app should be set to the Application Name (Step #4 in the Initial Setup above)

eg: 'docker'

3. env should be set to the lower case of your Beanstalk Environment name.

eg: 'docker-env'

4. Set the bucket_name. This can be found by searching for the S3 Storage service. Click the link for the elasticbeanstalk bucket that matches your region code and copy the name.

eg: 'elasticbeanstalk-us-east-1-923445599289'

5. Set the bucket_path to 'docker'

6. Set access_key_id to $AWS_ACCESS_KEY

7. Set secret_access_key to $AWS_SECRET_KEY



Create an IAM User
------------------
1. Search for the "IAM Security, Identity & Compliance Service"
2. Click "Create Individual IAM Users" and click "Manage Users"
3. Click "Add User"
4. Enter any name you’d like in the "User Name" field.

eg: docker-react-travis-ci

5. Click "Next"
6. Click "Attach Policies Directly"
7. Search for "beanstalk"
8. Tick the box next to "AdministratorAccess-AWSElasticBeanstalk"
9. Click "Next"
10. Click "Create user"
11. Select the IAM user that was just created from the list of users
12. Click "Security Credentials"
13. Scroll down to find "Access Keys"
14. Click "Create access key"
15. Select "Command Line Interface (CLI)"
16. Scroll down and tick the "I understand..." check box and click "Next"

Copy and/or download the Access Key ID and Secret Access Key to use in the Travis Variable Setup.



Travis Variable Setup
---------------------
1. Go to your Travis Dashboard and find the project repository for the application we are working on.
2. On the repository page, click "More Options" and then "Settings"
3. Create an AWS_ACCESS_KEY variable and paste your IAM access key from step #13 above.
4. Create an AWS_SECRET_KEY variable and paste your IAM secret key from step #13 above.

Deploying App
-------------
1. Make a small change to your src/App.js file in the greeting text.
2. In the project root, in your terminal run:

git add.
git commit -m “testing deployment"
git push origin main

3. Go to your Travis Dashboard and check the status of your build.
4. The status should eventually return with a green checkmark and show "build passing"
5. Go to your AWS Elastic Beanstalk application
6. It should say "Elastic Beanstalk is updating your environment"
7. It should eventually show a green checkmark under "Health". You will now be able to access your application at the external URL provided under the environment name.
```
***

## 90: `AWS Elastic Beanstalk`

`https://aws.amazon.com`

*My Account -> AWS Management Console

* AWS services -> `Elastic Beanstalk` -> Create application (docker-react-1) -> Web server env (workspace)

* Platform -> Docker

* Application Code -> Sample application

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
  region: "us-east-1"
  app: docker-react-1
  env: "Docker-env"
  bucket_name: elasticbeanstalk-us-east-1-826770578246              # S3 bucket_id
  bucket_path: "docker-react"                                       # `bucket_path:` name should be same as `app:`
  on:
    branch: master                                                  # `deploy:` once we push to branch `master`
```

NOTE: `app:` and `bucket_name:` names should be same

***

## 93: Required Update for `IAM` User and Keys


```
Required Update for IAM User and Keys
-------------------------------------
In the upcoming lecture, we will be creating an `IAM-user` and then generating a `key-pair` for deployment. 
There is a minor required change to this flow. Instead of being prompted to create a `key-pair` during the 
`IAM-user` creation, you **must** first create the `IAM-user`, then, create a `key-pair` associated with that user. 
AWS has also changed the terminology from Programmatic Access, to Command Line Interface (CLI).

Full updated instructions can be found below:
--------------------------------------------
1. Search for "IAM"
2. Click "Create Individual IAM-Users" and click "Manage Users"
3. Click "Add User"
4. Enter any name you’d like in the "User Name" field.
5. Click "Next"
6. ->Click "Attach Policies Directly"
7. Use the search bar to find and tick the box next to "AdministratorAccess-AWSElasticBeanstalk"
8. Click "Next"
9. Click "Create user"
---
10. Select the `IAM-user` that was just created from the list-of-users
11. -> Click "Security Credentials"
12. -> Scroll down to find "Access Keys"
13. -> Click "Create access key"
14. Select "Command Line Interface (CLI)"
15. Scroll down and tick the "I understand..." check box and click "Next"
16. -> Copy and/or download the Access_Key_ID and Secret_Access_Key to use for deployment.
```

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
  bucket_path: "docker-react"                                       # `bucket_path:` is same as `app:` name
  on:
    branch: master
  access_key_id: $AWS_ACCESS_KEY
  secret_access_key: "$AWS_SECRET_KEY"
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
COPY --from=builder /app/build /usr/share/nginx/html        # COPY --from=<phase> <src> <dst-on-nginx>
```

***

## 96: Workflow With GitHub

```
$ git checkout -b feature                       # switched to a new branch `feature`

$ git status
$ git add . 
$ git commit -m "updated App.js"
$ git push origin feature                       # push changes to `feature` branch

```

* `PR/merge branch`

***

## 97: Redeploy on `Pull Request (PR) Merge`

`Merge pull request` -> `Confirm merge`

***

## 98: Deployment Wrapup

***

## 99: Environment Cleanup

```
Environment Cleanup
-------------------
Remember, we need to `delete` the resources we created or you might end up paying real money for them. 
To clean up the `Elastic-Beanstalk-instance` we created, do the following:

1. Go to the `Elastic-Beanstalk-dashboard`.
2. In the left sidebar click `Applications`
3. Click the application you'd like to delete.
4. Click the `Actions` button and click `Delete Application`
5. You will be prompted to enter the name of your application to confirm the deletion.

Note: It might take a few minutes for the dashboard to update and show that your app is being deleted. 
Be a little patient!

```

***

## 100: AWS Configuration Cheat-Sheet

```
AWS Configuration Cheat Sheet
-----------------------------
This lecture note is not intended to be a replacement for the videos, but to serve as a cheat sheet for students who want to quickly run thru the AWS configuration steps or easily see if they missed a step. It will also help navigate through the changes to the AWS UI since the course was recorded.

Docker Compose config Update
----------------------------
Make sure to follow the steps in the earlier lecture note to rename your development docker compose file and create a new production compose file:

https://www.udemy.com/course/docker-and-kubernetes-the-complete-guide/learn/lecture/27975358

Create EC2 IAM Instance Profile
------------------------------
1. Go to AWS Management Console
2. Search for IAM and click the IAM Service.
3. Click Roles under Access Management in the left sidebar.
4. Click the Create role button.
5. Select AWS Service under Trusted entity type. Then select EC2 under common use cases.
6. Search for AWSElasticBeanstalk and select the AWSElasticBeanstalkWebTier, AWSElasticBeanstalkWorkerTier and AWSElasticBeanstalkMulticontainerDocker policies. Click the Next button.
7. Give the role the name of aws-elasticbeanstalk-ec2-role
8. Click the Create role button.

Create Elastic Beanstalk Environment
------------------------------------
1. Go to AWS Management Console
2. Search for Elastic Beanstalk and click the Elastic Beanstalk service.
3. If you've never used Elastic Beanstalk before you will see a splash page. Click the Create Application button. If you have created Elastic Beanstalk environments and applications before, you will be taken directly to the Elastic Beanstalk dashboard. In this case, click the Create environment button. There is now a flow of 6 steps that you will be taken through.
5. You will need to provide an Application name, which will auto-populate an Environment Name.
6. Scroll down to find the Platform section. You will need to select the Platform of Docker. You then must manually change from Docker running on 64bit Amazon Linux 2023 to Docker running on 64bit Amazon Linux 2.
7. Scroll down to the Presets section and make sure that free tier eligible has been selected:
8. Click the Next button to move to Step #2.
9. You will be taken to a Service Access configuration form.
Select Create and use new service role and name it aws-elasticbeanstalk-service-role. You will then need to set the EC2 instance profile to the aws-elasticbeanstalk-ec2-role created earlier (this will likely be auto-populated for you).
10. Click the Skip to Review button as Steps 3-6 are not applicable.
11. Click the Submit button and wait for your new Elastic Beanstalk application and environment to be created and launch.
12. Click the link below the checkmark under Domain. This should open the application in your browser and display a Congratulations message.

Update Object Ownership of `S3` Bucket
------------------------------------
1. Go to AWS Management Console
2. Search for S3 and click the S3 service.
3. Find and click the elasticbeanstalk bucket that was automatically created with your environment.
4. Click Permissions menu tab
5. Find Object Ownership and click Edit
6. Change from ACLs disabled to ACLs enabled. Change Bucket owner Preferred to Object Writer. Check the box acknowledging the warning.
7. Click Save changes.

Add AWS configuration details to `.travis.yml` file's deploy script
-----------------------------------------------------------------
1. Set the region. The region code can be found by clicking the region in the toolbar next to your username.

eg: 'us-east-1'

2. app should be set to the Application Name (Step #4 in the Initial Setup above)

eg: 'docker'

3. env should be set to the lower case of your Beanstalk Environment name.

eg: 'docker-env'

4. Set the bucket_name. This can be found by searching for the S3 Storage service. Click the link for the elasticbeanstalk bucket that matches your region code and copy the name.

eg: 'elasticbeanstalk-us-east-1-923445599289'

5. Set the bucket_path to 'docker'
6. Set access_key_id to $AWS_ACCESS_KEY
7. Set secret_access_key to $AWS_SECRET_KEY


Create an IAM-User
------------------
1. Search for the "IAM Security, Identity & Compliance Service"
2. Click "Create Individual IAM-Users" and click "Manage Users"
3. Click "Add User"
4. Enter any name you’d like in the "User Name" field.

eg: docker-react-travis-ci

5. Click "Next"
6. -> Click "Attach Policies Directly"
7. Search for "beanstalk"
8. -> Tick the box next to "AdministratorAccess-AWSElasticBeanstalk"
9. Click "Next"
10. Click "Create user"
11. Select the IAM user that was just created from the list of users
12. -> Click "Security Credentials"
13. -> Scroll down to find "Access Keys"
14. -> Click "Create access key"
15. Select "Command Line Interface (CLI)"
16. Scroll down and tick the "I understand..." check box and click "Next"

Copy and/or download the Access_Key_ID and Secret_Access_Key to use in the Travis Variable Setup.


Travis Variable Setup
---------------------
1. Go to your Travis Dashboard and find the project repository for the application we are working on.
2. On the repository page, click "More Options" and then "Settings"
3. Create an `AWS_ACCESS_KEY` variable and paste your IAM_access_key from step #13 above.
4. Create an `AWS_SECRET_KEY` variable and paste your IAM_secret_key from step #13 above.

Deploying App
-------------
1. Make a small change to your `src/App.js` file in the greeting text.
2. In the project root, in your terminal run:

```
git add.
git commit -m “testing deployment"
git push origin main
```

3. Go to your Travis Dashboard and check the status of your build.
4. The status should eventually return with a green checkmark and show "build passing"
5. Go to your `AWS Elastic Beanstalk` application
6. It should say "Elastic Beanstalk is updating your environment"
7. It should eventually show a green checkmark under "Health". You will now be able to access your application at the external URL provided under the environment name.
```

***

## 101: Finished Project Code with Updates Applied

***
