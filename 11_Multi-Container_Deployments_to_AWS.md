##  150. Multi-Container Definition Files

##  151. Finding Docs on Container Definitions

Google search: 
* Amazon ECS Task Definitions (https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task_definitions.html)
* Task Definition Parameters (https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task_definition_parameters.html)
* Container Definitions (https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task_definition_parameters.html#container_definitions)


##  152. Adding Container Definitions to DockerRun

create Dockerrun.aws.json

```
{
    "AWSEBDockerrunVersion" : 2,
    "containerDefinitions" : [
        {
            "name": "client",
            "image": "marshad1/multi-client",
            "hostname": "client"
        }
    ]
}
```

##  153. More Container Definitions

```
{
    "AWSEBDockerrunVersion" : 2,
    "containerDefinitions" : [
        {
            "name": "client",
            "image": "marshad1/multi-client",
            "hostname": "client",
            "essential": false
        },
        {
            "name": "server",
            "image": "marshad1/multi-server",
            "hostname": "api",
            "essential": false
        },
        {
            "name": "worker",
            "iamge": "marshad1/multi-worker",
            "hostname": "worker",
            "essential": false
        }
    ]
}
```

##  154. Forming Container Links

```
{
    "AWSEBDockerrunVersion" : 2,
    "containerDefinitions" : [
        {
            "name": "client",
            "image": "marshad1/multi-client",
            "hostname": "client",
            "essential": false
        },
        {
            "name": "server",
            "image": "marshad1/multi-server",
            "hostname": "api",
            "essential": false
        },
        {
            "name": "worker",
            "iamge": "marshad1/multi-worker",
            "hostname": "worker",
            "essential": false
        },
        {
            "name": "nginx",
            "image": "marshad1/multi-nginx",
            "hostname": "nginx",
            "essential": true,
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

Google Search: JSON validator

## 155. Creating the EB Environment

## 156. Managed Data Service Providers

## 157. Overview of AWS VPC's and Security Groups

## 158. RDS Database Creation

## 159. ElastiCache Redis Creation

## 160. Creating a Custom Security Group

## 161. Applying Security Groups to Resources

## 162. Setting Environment Variables

## 163. IAM Keys for Deployment

## 164. Travis Keys Update

##  165: Fix the missing bucket_name error

##  166: Travis Deploy Script

##  167. Container Memory Allocations

##  168. Verifying Deployment

##  169. A Quick App Change

## 170. Making Changes

## 171. Cleaning Up AWS Resources

##  172. AWS Configuration Cheat Sheet
