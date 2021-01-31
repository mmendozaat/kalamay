### CloudWatch

**Terminology**
- Metric - variable to monitor
  - standard
  - or custom - via agent or API
- Namespace
  - container for cloudwatch metrics
  - grouping of metrics
  - ex. AWS/EC2
- dimension - statistics
  - name, value pair
- period
  - time during which metric is collected
- alarm
  - initiates an action when a metric meets a specified condition
- events and logs

**states**
- ok
- alarm
- insufficient data

**event format**
- header fields 
  - constant
  - version
  - id
  - detail-type: description
  - source: aws.ec2
  - account
  - time
  - region
  - resources: [arn]
- detail fields
  - vary
  - detail: {
    instance-id: xxx,
    state: xxx
  }

**CloudTrail**
- event logs to s3
- optionally send logs in s3 to cloudwatch
- * determine what log means, what is the failure event

**AWS Config**
- get current status/configuration of resources
- history of configuration changes
- monitor configuration changes

**Q&A**
- EC2 instance Detailed Monitoring -  EC2 UI 
  - allows sending metric data every 1 minute
  - default is every 5 minutes
- cloud watch alarm can only watch a single metric
- cloud watch alarm preserves history for 2 weeks only
- cloud watch alarm - cannot change of existing alarm
- cloud watch alarm - # of periods * length of period cannot exceed one day
- familiarize with available metrics
- memory utilization of ec2 - custom only
- disk space on ec2 - custom only
- custom
  - things that are within the OS
- don't be too smart - obvious answers only - based on key words/hints

**Axioms**
- know which are custom and which are standard metrics
- cloudtrail - monitoring an api call
- read cloudwatch, cloudtrail faqs

### Domain 2: High Availability

#### Route 53
- DNS service
- register domain
- map domain with IP
- route

#### CloudFront
- CDN
- static, dynamic and video content
- optimized for performance and scale

#### ELB
- Application Load Balancer
  - HTTP/HTTPS
- Network Load Balancer
  - TCP
  - extreme performance and static IP for application
- Classic
  - for applications that use ec2-classic network
  - TCP, HTTP, HTTPS

  #### ASG
  - scale out to meet demand, scale in to reduce costs

  **Q&A**
  - what config is requied for ASG to terminate and replace instances that are unhealthy as detected by ELB
    - use ELB health checks
  - ASG by default use EC2 health checks
  - Route 53
    - Evaluate Target Health flag to true to maintain HA
    - ELB health checks are not accessible to Route 53

**Axioms**
- ASG+ELB+CW - work together for automatic add/remove of resources based on metrics
- HA by default
  - ELB, Route 53
- require configuration for HA
  - EC2, RDS
- load balancing
  - within region
    - ELB
  - across regions
    - Route 53 traffic flow