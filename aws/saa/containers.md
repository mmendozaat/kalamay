ECS
- elastic container service
- ecs cluster - across AZ
-- ecs service
--- ecs container instances (ec2)
---- container tasks

*ecs-cli
```
$ ecs-cli configure ... -> create cluster config 
$ ecs-cli configure ... -> create cli profile 
$ ecs-cli up --cluster-config <name> ... -> create the cluster
$ ecs-cli compose up ... -> creates containers and services (docker-compose.yml)
$ ecs-cli ps ... -> looks like docker command
$ ecs-cli down --cluster-config ... -> destroy cluster ... looks like docker-compose

$ aws ecs run-task ....
$ aws ecs stop-task ...

$ aws ecs create-cluster
$ aws ecs list-cluster
```
attach an instance to cluster
```
export ECS_CLUSTER=clustername > /etc/ecs/ecs.config
```

*pay only for instances or tasks

*ECS container agent - runs in EC2 instances
- included in ECS-optimized AMI

Launch Types
1. EC2
- classic - ec2 container instances
- ECR, Docker Hub, self-hosted
- roles required: IAM instance role, IAM task role
2. Fargate
- no container instances
- ECR, docker hub
- IAM task role
- uses cloudformation to create and delete

EKS
- elastic kubernetes service

ECR
- elastic container registry
- `$ aws ecr create-repository --repository-name ... --region ....`
- `$ aws ecr get-login --no-include-email --region ...`
- returns docker login
- run docker login

Auto scaling
1. service auto-scaling
- scale tasks
2. cluster auto-scaling
- scale ec2 instances
- use capacity provider associated with an auto-scaling group
