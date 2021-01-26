### Elastic Beanstalk

**Architecture Models**
1. Single instance
2. LB+ASG
3. ASG only - non web apps

**Components**
1. application
2. application version
3. environment - dev, test, prod

- allows rollback to previous version
- full control over lifecycle

create app
              > upload version > release to environments
create envs

supports:
- go
- java SE
- java/tomcat
-.net/iis
- node.js
- php
- python
- ruby
- packer builder
- docker
  - single
  - multiple
  - preconfigured
- custom

**Environment Types**
1. web server environment
2. worker environment

*cannot switch between load balancer types after creation

**deployment options**
1. all at once
- downtime
- no extra cost
- fastest
2. rolling 
- no extra cost
- performance degradation, below capacity
- set bucket size (num of instances to turn off at a time)
3. rolling with additional batches
- set bucket size (num of instances to add with new version at a time)
- keep everything running, run new set, turn off some instances replaced by new set
4. immutable
- deploy to totally new instances and ASG (temp)
- longest deployment
- highest cost - double capacity
- move instances from temp asg to asg, terminate old instances, remove temp asg

**blue-green deployment**
- not a feature of elastic beanstalk
- zero downtime
- create new stage environment (green), deploy new version there
- validate new green environment
- swap urls when verified
- use route 53 for weighted routing
- has dns changes 
  - elastic beanstalk provides ui for swapping url in environments

method | impact failed deploy | deploy time | zero downtime | no dns change | rollback process | code deployed to
--|--|--|--|--|--|--
all at once| downtime| fast | x | yes | manual redeploy | existing instances
rolling|single batch out of service|slow|yes|yes|manual redeploy| existing instances
rolling with addtl batch| minimal | slower|yes|yes|manual redeploy|existing instances
immutable|minimal|slowest|yes|yes|terminate new instances|new instances
blue/green|minimal|slowest|yes|x|swap url|new instances

**eb cli**
```
$ eb create
$ eb status
$ eb health
... events
... logs
... open
... deploy
... config
... terminate
```

**lifecycle policy**
- max 1000 application versions
- phase out
  - by age (in days)
  - by num versions limit (count of versions)
- option not to delete source bundle from s3
- define role to implement policy

**cicd**
- define parameters for application (parameters set in UI) in code
  - .ebextentions/ directory
  - yaml/jsn
  - .config extension
  - use `option_settings`
  - add resources such as RDS, ElastiCache, DynDB, etc
- can define cloudformation resources in ebextensions
- ex.
```
option_settings:
  aws:elasticbeanstalk:application:environment:
    DB_URL: "jdbc:..."
    DB_USER: username
```

**Cloning**
- cannot change much
- platform version can be changed
- cannot change ELB type
  - can change configuration

**Migration**
- create new environment
- deploy your application
- CNAME swap or Route 53 update

**RDS**
- can be provisioned in beanstalk
- do not provision in beanstalk - prod
  - lifecycle is tied to beanstalk
  - ok in dev, test

**Decouple RDS from beanstalk**
- snapshot RDS
- protect rds from deletion
- create new environment without RDS
  - point app to existing RDS
- perform CNAME swap/route 53 update - blue/green
- terminate cloud environment (RDS won't be deleted - really?)
- delete cloudformation stack (DELETE_FAILED state)

#### Docker
Single container
- provide
  - Dockerfile
  - or Dockerrun.aws.json (v1)
    - image
    - ports
    - volumes
    - logging, etc
- does not use ECS

Mutliple containers
- runs on ECS
- creates ECS
  - EC2 nodes
    - multiple containers per instance
    - ex
      - nginx
      - php,
      - other workers
  - Load Balancer
- requires
  - Dockerrun.aws.json (v2) at root
    - generate ECS task definition
  - image in ECR, docker hub, etc

#### HTTPS
- Load SSL certificate into load balancer
  - via console (load balancer configuration)
  - via code (.ebextensions/securelistener-alb.config)
- provision SSL certificate from ACM (AWS Certificate Manager) or CLI
- configure security group to allow incoming port 443 (HTTPS)
- redirect HTTP or HTTPS
  - configure redirect
  - configure ALB with a rule
  - make sure health checks are not redirected

**Web Server vs Worker Environment**
- worker tasks for long running jobs
- use cron.yaml
- worker tier
  - sqs > ec2 + asg
- web tier
  - elb > ec2 + asg

**Custom Platform**
- define from scratch
  - os
  - addtl software
  - scripts
- use only if app language is incompatible with Beanstalk and Docker
- how
  - define an ami - Platform.yaml
  - use Packer software to create AMIs
- custom platform vs custom AMI
  - platform - entirely new beanstalk platform
  - ami - use existing beanstalk platform, tweak