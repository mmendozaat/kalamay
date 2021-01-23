### CloudWatch
- performance/operations monitoring
- log events/operations
- high level
- log stored indefinitely
- alarm history - 14 days
- log retention - 1 day to 10 years
- log metric filters - search cloudtrail logs for specific terms, phrases, values

- Dashboard
- Alarms
  - alarm
  - insufficient
  - ok
  - billing
- Logs
  - log groups
    - log streams
      - log entries
  - insights
- Metrics
  - AWS Namespaces
  - Custom
- Events
  - rules
    - service, event type -> target service
- targets
  - ec2
  - lambda
  - kinesis data streams
  - delivery streams in kinesis data firehose
  - cloudwatch logs
  - ecs tasks
  - systems manager run
  - systems manager automation
  - aws batch
  - step functions
  - pipeline in codepipeline
  - codebuild
  - inspector assessment templates
  - SNS topics
  - SQS queues

Namespace
- container for metrics
- ex. AWS/ApiGateway

Metrics
- regional
- cannot be deleted
- expires after 15 months
- name, namespace, 0 to 10 dimensions (ex. by auto-scaling group, by region, etc)

Statistics
- metric data aggregations over time (min, max, sum, avg, etc)

Alarm
- over period of time
- notification to SNS or Auto Scaling policy

**no memory metrics in EC2 cloudwatch
- create script to get metric
- create cloudwatch agent role - attach to ec2 instance
- `aws cloudwatch put-metric-data --metric-name <name> --dimensions Instance=<instance> --namespace "Custom" --value <value>`

- cloudwatch log agent
  - send logs to cloudwatch
- unified cloudwatch agent
  - send logs and metrics to cloudwatch

**CloudWatch Metric Data Retention**
data point interval | available up to
--|--
< 60s | 3 hrs
1 minute | 15 days
5 minutes | 63 days
1 hour | 455 days (15 months)

### CloudTrail
- not enabled by default - create a trail
- security auditing
- log API activities
- low level granular
- log stored in cloudwatch/s3 indefinitely
- no native alarming, use cloudwatch
- log to s3
- single trail across regions
- or single trail per region
- control plane operations
  - management operations
- data plane operations
  - data events
  - high volume
  - s3, lambda
  
redrive policy sqs?

AWS Config
- configuration history of all resources
- compare config with desired config
- sns notification - config changes
- configs stored in s3
- export complete inventory of aws resources
- compliance auditing, security analysis etc
- configuration management as part of ITIL
- configuration timeline
- compliance timeline
- predefined or custom remediation action
