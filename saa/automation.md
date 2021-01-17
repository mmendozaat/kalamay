#### CloudFormation

- infrastructure as code
- defined in a template
- provides a visual graph
- deploy any AWS service
- json or yaml
- can deploy elastic beanstalk
- similar to terraform

terminology
- templates - code to create resources
- stacks - physical resources created
- stacksets
  - stacks across regions and accounts
- changesets
  - proposed changes to stacks

nested stacks
- reuse code/templates

detect drift
- detect if resources match template

update stacks
- direct update
- changesets

#### Elastic Beanstalk
- Paas service
- provides multi-AZ, auto-scaling and load-balancing
- deploys apps in EC2 as stack in cloudformation
  - java
  - .net
  - php
  - node.js
  - python
  - ruby
  - go
  - docker
- use zip or war files
- similar to google app engine
- uses cloudformation
- logical ids - template names
- physical ids - aws ids
- full control of underlying resources
- enable versioning
- git integration ?


Create application -> environment

Create environment
1. web server
2. worker

can define your own keypair to ssh to instance
