##  146. Multi-Container Definition Files

Project Directory -> `Dockerrun.aws.json` -> Container Definitions

* `Dockerrun.aws.json`: Already build the image, no build required

***

##  147. Finding Docs on Container Definitions

* `Elastic Beanstalk` calls -> `Amazon Elastic Container Service (ECS)` {task_def_1, task_def_2, ...}

Google search: 
* [Amazon ECS Task Definitions](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task_definitions.html)
* [Task Definition Parameters](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task_definition_parameters.html)
* [Container Definitions](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task_definition_parameters.html#container_definitions)

***

## 148. Adding Container Definitions to DockerRun

Create `Dockerrun.aws.json`

```
{
    "AWSEBDockerrunVersion" : 2,                    # AWS eb Docker run Version
    "containerDefinitions" : [                      # Notice plural
        {
            "name": "client",
            "image": "muarshad01/multi-client",
            "hostname": "client"
        }
    ]
}
```

***

## 149. More Container Definitions

```
{
    "AWSEBDockerrunVersion" : 2,
    "containerDefinitions" : [
        {
            "name": "client",
            "image": "muarshad01/multi-client",
            "hostname": "client",
            "essential": false                      # essential `true` means that this must be up
        },
        {
            "name": "server",
            "image": "muarshad01/multi-server",
            "hostname": "api",
            "essential": false
        },
        {
            "name": "worker",
            "iamge": "muarshad01/multi-worker",
            "hostname": "worker",
            "essential": false
        }
    ]
}
```

***

## 150. Forming Container Links

```
{
    "AWSEBDockerrunVersion" : 2,
    "containerDefinitions" : [
        {
            "name": "client",
            "image": "muarshad01/multi-client",
            "hostname": "client",
            "essential": false
        },
        {
            "name": "server",
            "image": "muarshad01/multi-server",
            "hostname": "api",                      # Notice: `name` and `hostname` are different here
            "essential": false
        },
        {
            "name": "worker",
            "iamge": "muarshad01/multi-worker",
            "hostname": "worker",
            "essential": false
        },
        {
            "name": "nginx",
            "image": "muarshad01/multi-nginx",
            "hostname": "nginx",
            "essential": true,                      # essential `true`
            "portMappings": [
                {
                    "hostPort": 80,
                    "containerPort": 80
                }
            ],
            "links": ["client", "server"]
        }
    ]
}
```

* Google Search: [JSON validator](https://jsonlint.com/)

***

## 151. Required `AWS Elastic Beanstalk` Environment Setup Updates

```
The process for creating an Elastic Beanstalk environment has changed (this is very common, as the AWS UI changes frequently). There is now a flow of 6 steps that you will be taken through.

Step 1:
-------
You will need to select the Platform of Docker. 
You then must manually change from Docker running on 64bit Amazon Linux 2023 to Docker running on 64bit Amazon Linux 2. 
The 2023 platform was just released in early August and is currently not compatible with our CI/CD automation.
Scroll down to the Presets section and make sure that free tier eligible has been selected:


Click the Next button to move to Step #2.

Step 2:
------
You will be taken to a Service Access configuration form. 
Here, you should select Use an existing service role, which will auto-select the aws-elasticbeanstalk-service-role and auto-populate the aws-elasticbeanstalk-ec2-role as the instance profile:
You may now click the Skip to Review button as Steps 3-6 are not applicable.
Click the Submit button and wait for your new EBS application and environment to be created and launch.

Required Updates for Docker Compose:

1. Rename the current docker-compose file
Rename the `docker-compose.yml` file to `docker-compose-dev.yml`. 
Going forward you will need to pass a flag to specify which compose file you want to build and run from:

$ docker-compose -f docker-compose-dev.yml up
$ docker-compose -f docker-compose-dev.yml up --build
$ docker-compose -f docker-compose-dev.yml down

2. Create a production-only `docker-compose.yml` file
The production compose file will follow closely what was written in the `Dockerrun.aws.json`. 
There are two major differences:
-- No Container Links: In the "Forming Container Links" lecture we add the client and server services to the links array of the nginx service. Docker Compose will handle this container communication automatically for us.
-- Environment Variables: When using a compose file we will need to explicitly specify the environment variables each service will need access to. The value for each variable must match the corresponding variable names you have specified in the Elastic Beanstalk environment. The AWS variables are created in the "Setting Environment Variables" lecture.
-- Note - You must not have a `Dockerrun.aws.json` file in your project directory. If AWS EBS sees this file the deployment will fail. If you have previously followed this course and deployed to the old Multi-container platform you will need to delete this file before moving to the new platform!!!
```

Complete `docker-compose.yml` file:

```
version: "3"
services:
  client:
    image: "cygnetops/multi-client-10-14"
    mem_limit: 128m
    hostname: client
  server:
    image: "cygnetops/multi-server-10-14"
    mem_limit: 128m
    hostname: api
    environment:
      - REDIS_HOST=$REDIS_HOST
      - REDIS_PORT=$REDIS_PORT
      - PGUSER=$PGUSER
      - PGHOST=$PGHOST
      - PGDATABASE=$PGDATABASE
      - PGPASSWORD=$PGPASSWORD
      - PGPORT=$PGPORT
  worker:
    image: "cygnetops/multi-worker-10-14"
    mem_limit: 128m
    hostname: worker
    environment:
      - REDIS_HOST=$REDIS_HOST
      - REDIS_PORT=$REDIS_PORT
  nginx:
    image: "cygnetops/multi-nginx-10-14"
    mem_limit: 128m
    hostname: nginx
    ports:
      - "80:80"
```
***

## 152. AWS Configuration Cheat Sheet - Updated for new UI

This lecture note is not intended to be a replacement for the videos, but to serve as a cheat sheet for students who want to quickly run thru the AWS configuration steps or easily see if they missed a step. It will also help navigate through the changes to the AWS UI since the course was recorded.

### Elastic Beanstalk Application Creation

1. Make sure you have followed the guidance in this note.

2. Go to `AWS Management Console` and use Find `Services` to search for `Elastic Beanstalk`

3. Click `Create Application`

4. Set `Application Name` to `multi-docker`

5. Scroll down to `Platform` and select `Docker`

6. Verify that `Single Instance (free tier eligible)` has been selected

7. Click the `Next` button.

8. In the `Service Role` section, verify that `Use an Existing service role` is selected.

9. Verify that `aws-elasticbeanstalk-service-role` has been `auto-selected` for the service role.

10. Verify that `aws-elasticbeanstalk-ec2-role` has been `auto-selected` for the instance profile.

11. Click `Skip to review` button.

12. Click the `Submit` button.

13. You may need to refresh, but eventually, you should see a green checkmark underneath Health.

### RDS Database Creation

1. Go to `AWS Management Console` and use Find `Services` to search for `RDS`

2. Click `Create database` button

3. Select `PostgreSQL`

4. In `Templates`, check the `Free tier` box

5. Scroll down to `Settings`.

6. Set `DB Instance identifier` to `multi-docker-postgres`

7. Set `Master Username` to `postgres`

8. Set `Master Password` to `postgrespassword` and confirm.

9. Scroll down to Connectivity. Make sure `VPC` is set to `Default VPC`

10. Scroll down to Additional Configuration and click to unhide.

11. Set Initial database name to `fibvalues`

12. Scroll down and click Create Database button

### `ElastiCache Redis` Creation

1. Go to `AWS Management Console` and use Find `Services` to search for `ElastiCache`

2. In the sidebar under `Resources`, click `Redis Clusters`

3. Click the `Create Redis` cluster button

4. Select `Configure` and `create a new cluster`

5. Make sure `Cluster Mode is DISABLED`.

6. Scroll down to `Cluster info` and set Name to `multi-docker-redis`

7. Scroll down to `Cluster settings` and change Node type to `cache.t2.micro`

8. Change Number of Replicas to `0` (Ignore the warning about Multi-AZ)

9. Scroll down to `Subnet group`. Select `Create a new subnet` group if not already selected.

10. Enter a name for the Subnet Group such as `redis`.

11. Scroll down and click the `Next` button

12. Scroll down and click the `Next` button again.

13. Scroll down and click the `Create button`.

### Creating a `Custom Security Group`

1. Go to `AWS Management Console` and use Find `Services` to search for `VPC`

2. Find the `Security` section in the left sidebar and click `Security Groups`

3. Click `Create Security Group` button

4. Set `Security group name` to `multi-docker`

5. Set `Description` to `multi-docker`

6. Make sure `VPC` is set to your `default VPC`

7. Scroll down and click the `Create Security Group` button.

8. After the security group has been created, find the Edit `inbound rules` button.

9. Click `Add Rule`

10. Set `Port Range` to 5432-6379

11. Click in the box next to Source and start typing `sg` into the box. Select the Security Group you just created.

12. Click the Save rules button

### Applying `Security Groups` to `ElastiCache`

1. Go to `AWS Management Console` and use Find `Services` to search for `ElastiCache`

2. Under `Resources`, click `Redis clusters` in Sidebar

3. Check the box next to your `Redis cluster`

4. Click `Actions` and click `Modify`

5. Scroll down to find Selected security groups and click Manage

6. Tick the box next to the new `multi-docker` group and click Choose

7. Scroll down and click Preview Changes

8. Click the Modify button.

### Applying `Security Groups` to `RDS`

1. Go to `AWS Management Console` and use Find `Services` to search for `RDS`

2. Click `Databases` in Sidebar and check the box next to your instance

3. Click Modify button

4. Scroll down to Connectivity and add select the new `multi-docker` security group

5. Scroll down and click the Continue button

6. Click Modify DB instance button

### Applying `Security Groups` to `Elastic Beanstalk`

1. Go to `AWS Management Console` and use Find `Services` to search for `Elastic Beanstalk`

2. Click `Environments` in the left sidebar.

3. Click `MultiDocker-env`

4. Click `Configuration`

5. In the Instances row, click the Edit button.

6. Scroll down to `EC2 Security Groups` and tick the box next to `multi-docker`

7. Click Apply and Click Confirm

8. After all the instances restart and go from No Data to Severe, you should see a green checkmark under Health.

### Add AWS configuration details to `.travis.yml` file's deploy script

1. *Set the region*. The region code can be found by clicking the region in the toolbar next to your username.
eg: 'us-east-1'

2. `app` should be set to the Elastic Beanstalk Application Name
eg: `multi-docker`

3. `env` should be set to your Elastic Beanstalk Environment name.
eg: `MultiDocker-env`

4. Set the `bucket_name`. This can be found by searching for the `S3` Storage service. Click the link for the `elasticbeanstalk` bucket that matches your region code and copy the name.
eg: 'elasticbeanstalk-us-east-1-923445599289'

5. Set the `bucket_path` to `docker-multi`

6. Set `access_key_id` to `$AWS_ACCESS_KEY`

7. Set `secret_access_key` to `$AWS_SECRET_KEY`

### Setting `Environment Variables`

1. Go to `AWS Management Console` and use Find `Services` to search for `Elastic Beanstalk`

2. Click `Environments` in the left-sidebar

3. Click `MultiDocker-env`

4. In the left-sidebar click `Configuration`

5. Scroll down to the `Updates, monitoring, and logging` section and click Edit.

6. Scroll down to the `Environment Properties` section. Click Add environment property.

7. In another tab Open up `ElastiCache`, click `Redis` and check the box next to your cluster. Find the Primary Endpoint and copy that value but omit the :6379

8. Set `REDIS_HOST` key to the primary endpoint listed above, remember to omit :6379

9. Set `REDIS_PORT` to `6379`

10 Set `PGUSER` to `postgres`

11. Set `PGPASSWORD` to `postgrespassword`

12. In another tab, open up the RDS dashboard, click databases in the sidebar, click your instance and scroll to Connectivity and Security. Copy the endpoint.

13. Set the `PGHOST` key to the endpoint value listed above.

14. Set `PGDATABASE` to `fibvalues`

15. Set `PGPORT` to `5432`

16. Click Apply button

17. After all instances restart and go from No Data, to Severe, you should see a green checkmark under Health.

### IAM Keys for Deployment

You can use the same `IAM User's` `access_key` and `secret_key` from the single container app we created earlier, or, you can create a new IAM user for this application:

1. Search for the `IAM Security, Identity & Compliance Service`

2. Click `Create Individual IAM Users` and click `Manage Users`

3. Click `Add User`

4. Enter any name you’d like in the `User Name` field
eg: docker-multi-travis-ci

5. Click `Next`

6. Click `Attach Policies Directly`

7. Search for `beanstalk`

8. Tick the box next to `AdministratorAccess-AWSElasticBeanstalk`

9. Click `Next`

10. Click `Create user`

11. Select the `IAM user` that was just created from the list of users

12. Click `Security Credentials`

13. Scroll down to find `Access Keys`

14. Click `Create access key`

15. Select `Command Line Interface (CLI)`

16. Scroll down and tick the "I understand..." check box and click "Next"

Copy and/or download the `Access_Key_ID` and `Secret_Access_Key` to use in the Travis Variable Setup.

### AWS Keys in Travis

1. Go to your `Travis Dashboard` and find the `project repository` for the application we are working on.

2. On the repository page, click `More Options` and then `Settings`

3. Create an `AWS_ACCESS_KEY` variable and paste your `IAM access key`

4. Create an `AWS_SECRET_KEY` variable and paste your `IAM secret key`

### Deploying App

1. Make a small change to your `src/App.js` file in the greeting text.

2. In the project root, in your terminal run:

```
$ git add.
$ git commit -m “testing deployment"
$ git push origin main
```

3. Go to your `Travis Dashboard` and check the status of your build.

4. The status should eventually return with a green checkmark and show "build passing"

5. Go to your `AWS Elastic Beanstalk` application

6. It should say "Elastic Beanstalk is updating your environment"

7. It should eventually show a green checkmark under "Health". You will now be able to access your application at the external URL provided under the environment name.

***

## 153. Creating the Elastic Beanstalk Environment

* `aws.amazon.com` -> `AWS Management Console` -> Login

2. Go to `AWS Management Console` and use Find `Services` to search for `Elastic Beanstalk`

3. Click `Create application`

4. Set Application Name to `multi-docker`

5. Scroll down to `Platform` and select `Docker`

6. Verify that `Single Instance (free tier eligible)` has been selected

7. Click the Next button.

### Configure Service Access

8. In the `Service Role` section, verify that `Use an Existing service role` is selected.

9. Verify that `aws-elasticbeanstalk-service-role` has been `auto-selected` for the service role.

10 Verify that `aws-elasticbeanstalk-ec2-role` has been `auto-selected` for the instance profile.

11. Click Skip to review button.

12. Click the Submit button.

* You may need to refresh, but eventually, you should see a green checkmark underneath Health.

***

## 154. Managed Data Service Providers

***

## 155. Overview of `AWS VPC's` and `Security Groups`

1. Go to `AWS Management Console` and use Find `Services` to search for `VPC`

2. Find the `Security` section in the left sidebar and click `Security Groups`

3. Click `Create Security Group` button

4. Set Security group name to `multi-docker`

5. Set Description to `multi-docker`

6. Make sure `VPC` is set to your `default VPC`

7. Scroll down and click the `Create Security Group` button.

8. After the security group has been created, find the `Edit inbound rules` button.

9. Click Add Rule

10. Set `Port Range` to `5432-6379`??

11. Click in the box next to Source and start typing `sg` into the box. Select the Security Group you just created.

12. Click the Save rules button

***

## 156. `RDS` Database Creation

1. Go to `AWS Management Console` and use Find `Services` to search for `RDS`

2. Click `Create database` button

3. Select `PostgreSQL`

4. In `Templates`, check the `Free tier` box

5. Scroll down to `Settings`.

6. Set `DB Instance identifier` to `multi-docker-postgres`

7. Set `Master Username` to `postgres`

8. Set `Master Password` to `postgrespassword` and confirm.

9. Scroll down to Connectivity. Make sure `VPC` is set to `Default VPC`

10. Scroll down to Additional Configuration and click to unhide.

11. Set Initial database name to `fibvalues`

12. Scroll down and click Create Database button

***

## 157. ElastiCache Redis Creation

1. Go to `AWS Management Console` and use Find `Services` to search for `ElastiCache`

2. In the sidebar under `Resources`, click `Redis Clusters`

3. Click the `Create Redis` cluster button

4. Select `Configure` and `create a new cluster`

5. Make sure `Cluster Mode is DISABLED`.

6. Scroll down to `Cluster info` and set Name to `multi-docker-redis`

7. Scroll down to `Cluster settings` and change Node type to `cache.t2.micro`

8. Change Number of Replicas to `0` (Ignore the warning about Multi-AZ)

9. Scroll down to `Subnet group`. Select `Create a new subnet` group if not already selected.

10. Enter a name for the Subnet Group such as `redis`.

11. Scroll down and click the `Next` button

12. Scroll down and click the `Next` button again.

13. Scroll down and click the `Create button`.

***

## 158. Creating a Custom Security Group

1. Go to `AWS Management Console` and use Find `Services` to search for `VPC`

2. Find the `Security` section in the left sidebar and click `Security Groups`

3. Click `Create Security` Group button

4. Set Security group name to `multi-docker`

5. Set Description to `multi-docker`

6. Make sure `VPC` is set to your `default VPC`

7. Scroll down and click the `Create Security Group` button

8. After the security group has been created, find the `Edit inbound rules` button

9. Click Add Rule

10. Set `Port Range` to 5432-6379

11. Click in the box next to Source and start typing `sg` into the box. Select the Security Group you just created.

12. Click the Save rules button

***

## 159. Applying Security Groups to Resources

***

## 160. Setting Environment Variables

1. Go to `AWS Management Console` and use Find `Services` to search for `Elastic Beanstalk`

2. Click `Environments` in the left-sidebar

3. Click `MultiDocker-env`

4. In the left-sidebar click `Configuration`

5. Scroll down to the `Updates, monitoring, and logging` section and click Edit.

6. Scroll down to the `Environment Properties` section. Click Add environment property.

7. In another tab Open up `ElastiCache`, click `Redis` and check the box next to your cluster. Find the Primary Endpoint and copy that value but omit the :6379

8. Set `REDIS_HOST` key to the primary endpoint listed above, remember to omit :6379

9. Set `REDIS_PORT` to `6379`

10 Set `PGUSER` to `postgres`

11. Set `PGPASSWORD` to `postgrespassword`

12. In another tab, open up the RDS dashboard, click databases in the sidebar, click your instance and scroll to Connectivity and Security. Copy the endpoint.

13. Set the `PGHOST` key to the endpoint value listed above.

14. Set `PGDATABASE` to `fibvalues`

15. Set `PGPORT` to `5432`

16. Click Apply button

17. After all instances restart and go from No Data, to Severe, you should see a green checkmark under Health.

***

## 161. IAM Keys for Deployment

***

## 162. Travis Keys Update

***

## 163: Travis Deploy Script

***

## 164. Container Memory Allocations

***

##  165. Verifying Deployment

***

##  166. A Quick App Change

***

## 167. Making Changes

***

## 168. Cleaning Up AWS Resources

* `Services` -> `ElasticBeanstalk` -> select `Application name` -> Actions (`Delete application`)

* `Services` -> `RDS` -> `DB Instances` -> select `DB Identifier` -> Actions (`Delete me`) 

* `Services` -> `ElasticCache` ->

* `Services` -> `VPC` -> `Security Groups` -> select ??? -> Actions (`Delete`)

* `Services` -> `IAM` -> `Users` -> select ??? -> Actions (`Delete`)

***

## 169. AWS Configuration Cheat Sheet

***

## 170. Finished Project Code with Updated Applies

***
