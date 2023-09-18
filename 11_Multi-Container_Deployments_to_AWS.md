##  146. Multi-Container Definition Files

***

##  147. Finding Docs on Container Definitions

Google search: 
* [Amazon ECS Task Definitions](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task_definitions.html)
* [Task Definition Parameters](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task_definition_parameters.html)
* [Container Definitions](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task_definition_parameters.html#container_definitions)

***

## 148. Adding Container Definitions to DockerRun

Create `Dockerrun.aws.json`

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

***

## 149. More Container Definitions

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

***

## 150. Forming Container Links

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

***

## 151. Required AWS Elastic Beanstalk Environment Setup Updates

***

## 152. AWS Configuration Cheat Sheet - Updated for new UI

***

## 153. Creating the Elastic Beanstalk Environment

***

## 154. Managed Data Service Providers

***

## 155. Overview of AWS VPC's and Security Groups

***

## 156. RDS Database Creation

***

## 157. ElastiCache Redis Creation

***

## 158. Creating a Custom Security Group

***

## 159. Applying Security Groups to Resources

***

## 160. Setting Environment Variables

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

***

## 169. AWS Configuration Cheat Sheet

***

## 170. Finished Project Code with Updated Applies

***
