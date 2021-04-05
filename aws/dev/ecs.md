### ECS - elastic container service

ECS cluster
- has ASG
- each EC2 in cluster
  - ECS agent
  - env var 
    - ECS_CLUSTER=<clustername>
    - ECS_BACKEND_HOST
      - also in `/etc/ecs/ecs.config`

ECS Task Definitions
- like k8 yml
- how to run a docker container
  - image
  - ports
  - memory,cpu
  - env vars
  - network
- Task can have its own IAM role
- hostPort = 0 (blank in UI)
  - port will be assigned

ECS service
- manages tasks across ECS cluster
- replica or daemon
  - replica
    - N tasks
  - daemon
    - 1 task
- deployments
  - rolling
  - blue/green 
    - via codeDeploy
- mutiple container - dynamic port mapping
  - use an ALB
    - define in EC2 UI

ECR
- `aws ecr get-login --no-include-email`
- `aws ecr get-login-password | docker login --username AWS --password-stdin <ecr url>`

Fargate
- task size
  - memory - from 0.5GB, increments of 0.5GB
  - vCPU - 0.25, increments of 0.25
- no host port mapping - target port only 

IAM roles
- EC2 instance profile roles
  - used by ECS agent
  - `AmazonEC2ContainerServicefoEC2Role`
- ECS service role
  - `AmazonEC2ContainerServiceRole`
  - `ec2:...`
  - `elasticoadbalancing:...`
- ECS service autoscale
  - `AmazonECSServiceRolePolicy`
  - `ec2:...`
  - `elasticloadbalancing:...`
  - `route53:...`, etc
- ECS Task role
  - roles specific for task container
  - `AmazonECSTaskExecutionRolePolicy`
  - `ecr:...`
  - `logs:...`

ECS Task Placement
- best effort
  - satisfy CPU, memory, port, etc
  - satisfy constraints
  - satisfy task placement strategy
- types
  - binpack
    - based on cpu/memory available (field)
    - pack an instance first
  - random
  - spread
    - across specified value
      - ex: AZ, instance id (field)
      - "field": "attribute:ecs.availability-zone"
  - mixed
- constraints
  - distinctInstance
    - each task on different container instance
  - memberOf
    - satisfy an expression
    - "expression": "attribute:ecs.instance-type =~ t2.*"

ECS Service Auto Scaling
- task level scaling != instance level scaling
- TargetTracking
  - average CloudWatch metric
- Step Scaling
  - scale based on CloudWatch alarm
- Scheduled Scaling

Capacity Providers
- cluster auto scaling
- defint a target metric
- associate an ASG