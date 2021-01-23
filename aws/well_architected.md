### Well-Architected

Components
1. questions
2. pillars
3. design principles

#### design principles

old way | cloud way
--|--
guess infrastructure needs | no need to guess
cannot test at scale | test at scale
cannot justify experiments | make experimentation easier
architecture frozen in time | allow for evolving architectures
|-| build data-driven architectures

#### Pillars

1. Operational excellence
- run and monitor systems to deliver business value
- Focus areas
  - organization
  - prepare - design for operations
  - operate - 
    - how to operate
    - understand health of operations and workload
    - identify risks and respons appropriately
  - evolve
    - feedback loops
    - making improvements
2. Security
- ability to protect information, systems and assets
- focus areas
  - identity and access management
  - detection
  - infrastructure protection
  - data protection
  - incident response
3. Reliability
- ability to recover from failures
- focus areas
  - foundations
  - workload architecture
  - change management
  - failure management
4. Performance efficiency
- ability to use resources efficiently
  - selection of resources
  - review resources
  - monitoring
  - trade-offs to maximize performance
5. Cost optimization
- ability to achieve business outcomes at the lowest price point
  - practice cloud financial management
  - expenditure and usage awareness
  - cost effective-resources
  - manage demand and supply resources
  - optimize over time
  
Use Well-Architected Framework
- build cloud-native architecture
- build a backlog
- gating mechanism before launch
- maturity of teams
- due diligence for acquisition

### Operational Excellence

Focus areas:
1. Organization
2. Prepare for operations
3. Operate
4. Evolve workload
- feedback loops
- making improvements

#### Design Principles
- operations as code
- frequent and small, reversible changes
- refine operations frequently
- anticipate failure
- learn from operational failures

#### Best Practice
- operational priorities
  - involve business and development teams
  - compliance requirements
  - trade-offs
- operating model
- organizational culture

*Prepare* 
- design telemetry
- improve flow
- mitigate deployment risks
- understand operational readiness

- developers - individual sandboxes and dev environments
- use infrastructure as code + configuration management systems
- increase controls
- turn off environments when not in use

*operate*
- understand workload health
- understand operations health
- runbooks/playbooks - consistent response to events
  - define triggers for escalation
  - process for escalation
  - owners of each action
- event, incident and problem management process
  - event - observation 
  - incident - event that requires a response
  - problems - incident that recurs or cannot be resolved

*evolve*
- learn from experience
- make improvements
- share learning

best practice
- feedback loops
  - identify areas for improvements
  - prioritize improvements
  - execute
  - evaluate improvement

**Key Points**
1. understand business priorities
2. design for operations
3. evaluate operational readiness
4. understand workload and operational health
5. prepare for and respond to events
6. learn from experience, share learnings and make improvements

### Security Pillar

- identity and access management
- detection
- infrastructure protection
- data protection
- incident response

#### design principles

- implement a strong identity foundation
- use fine-grained access controls
- apply security at all layers
- automate security best practices
- prepare for security events and automation

#### best practice

**iam - identities**
- strong sign-in mechanisms
- centralized identity provider
- secure secrets

**iam - permissions**
- define access requirements
- grant least privilege
- limit public and cross-account access
- reduce permissions continuously

**detective controls**
- to identify or detect a security event
  - lifecycle controls to establish operational baselines
  - internal auditing to examine controls
  - automated alerting
- automate misconfiguration detection and response - aws config
  - conformance packs for aws config
    - configure rules and automated remediation
    - automated playbook
    
 **infrastructure protection**
 - trust boundaries
 - security system configuration
 - os hardening
 - policy enforcement points
 - best practice
    - control traffic at all layers
      - cloudfront with WAF
      - divide vpc into subnets
      - use all available controls
    - use managed services

**data protection**
- data classification
  - classify data at rest
- keep people away from data
  - dashboard instead of direct access
  - cicd pipeline
- protection in transit
- best practice
  - encrypt in transit and at rest

**incident response**
- best practice
  - easy way to gain access in case of an event
  - right tools pre-deployed
  - conduct game days regularly
  - create new trusted environment to conduct your investigations
    - use cloudfront templates + ebs snapshots t0 setup environment 
- keypoints
  - protecting information, systems, and assets
  - keeping root account credentials protected
  - encrypting data at rest and in transit on AWS if applicable
  - ensuring only authenticated and authorized users are able to access your resources
  - use detective controls to detect/identify a security breach
  
### Reliability Pillar
- ability of a workload to perform its intended function correctly and consistently

**features**
- recover from disruptions - high availability
- dynamically acquire resources to meet demand - scale
- mitigate disruptions - fault tolerance

#### recover from failures
- foundations
- workload architecture
- change management
- failure management

**design principles**
- automatically recover from failure
- test recovery procedures
- scale horizontally
- stop guessing capacity
- manage change in automation

foundations
- must have sufficient network bandwidth
- service quotas/limits
  - aws default limits
  - fixed limits (design constraints)

workload architecture
- distributed systems
  - use service-oriented architecture / microservices
  - design to prevent failures
  - design to mitigate failures
    - idempotent request/response
    - graceful degradation
    - fail fast
    - limit queues
    - throttle requests

change management
- workload monitoring
- SNS
- review
- cloudwatch
- automatic scaling to match demand
- deployment
  - use runbooks
  - integrate testing as part of deployment
  - use immutable infrastruture
  - blue-green deployment - weighted traffic - increasing towards green deployments
    - blue - existing environment
    - green - new production environment
    
failure management
- automate response
  - replace with new one
  - analyze failed resource separately
- automated testing to verify full recovery process  
- fault isolation
  - establish boundaries
    - multiple AZs
    - multiple AWS regions
    - bulkhead architecture
      - for data
        - shards/partitions
      - for services
        - cells
      - failure only affects a portion of requests
- best practice
  - backup and DR
    -define objectives
    - backup strategy
      - logical backup
      - physical backup
    - periodic recover testing
    - automated recovery
    - periodic reviews
      - rto, rpo
  - test reliability
    - use playbooks
      - identify factors that constribute to a failure scenario
    - test function and performance
    - use chaos engineering
      - inject failures regularly into preprod, prod environments
      - hypothesize effect and compare with actual
      
### Performance Efficency Pillar
- use resources efficiently

*Focus areas*
- selection
- review
- monitoring
- trade-offs

*design principles*
- democratize advanced technologies
- go global in minutes
- use serverless architectures
- experiment more often
- consider mechanical sympathy

#### selection
- select appropriate resource types
- benchmark and load test
  - different sizes and features
- monitor performance

#### review
- experriment with new services/features
  - ex. load testing
  - use cloudformation
  - experiment in a low-risk way

#### monitoring
- monitor performance
- automate actions around performance issues
  - cloudwatch
  - kinesis
  - SQS
  - lambda
- performance alarms

#### trade-offs
- trade-off memory/storage with compute
- position resources
- add read-only replicas
- measure impact
- proximity and caching
  - cdn - cloudfront
  - database caching - elasticache + rds
  - reduce latency
  - proactive monitoring and notification

### Cost Optimization Pillar

**Focus areas**
- cloud financial management
- expenditure and usage awareness
- cost-effective resources
- manage demand and supply resources
- optimize over time

**Design Principles**
- cloud financial management
- adopt a consumption model
- measure overall efficiency
- anayze and attribute expenditure
- stop spending money on undifferentiated heavy lifting

**best practice**
- cost-effective resources
  - right size instance vs compute time
  - use manage service
- pricing model
  - on-demand
  - savings plans and reserved instances
  - spot instances
    - bid > market
    - uninterrupted spot blocks up to 6 hours
- managed services
  - application-level services
  - automation can reduce costs
- manage supply and demand
  - provision to match demand
  - auto-scaling
  - monitoring tools
  - benchmarking
  - manage demand with a queue or buffer
  - auto-scale
    - demand-based
    - time-based
- expenditure awareness
  - tag resources
  - track project lifecycle and profile applications
  - monitor usage and spend
  - aws cost explorer
  - partner tools
- optimize over time
  - reassess existing architecture and try new services or features that are more cost effective
  - decommission resources actively
  - stay up to date on new features and services
  
**Key Points**
- avoiding or eliminating unneeded cost or suboptimal resources
- measuring overall workload efficiency
- managing demand and supply resources
- using savings plans to reduce cost
- measuring overall efficiency
- learning about new services and features

### Well-Architected Review
- do continually
- benefits
  - build and deploy faster
  - lower and mitigate risks
  - make informed decisions
  - learn aws best practices

### Well-Architected Tool
- Workload
  - set of components that deliver business value
- save milestones - ex  v1.0
- answer questions
- provides improvement plan
  - reorder priority
  - steps to take
- generate pdf report
  - answers to question 
  - risks identified
- dashboard
  - completed workload reviews
    - high and medium risks
  - milestones
