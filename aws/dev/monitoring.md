### CloudWatch

namespaces
- group of metrics
- dimensions
  - up to 10 per metric

ec2
- default - every 5 minutes
- detailed monitoring - $ - every 1 minute
- free tier  
  - 10 detailed monitoring metrics
- memory - no metric
  - must be custom metric
- custom metric
  - standard: 1 minute
  - high resolution - 1s (StorageResolution API parameter)
  - call `PutMetricData`

ASG
- enable group metrics collection

alarm
- period
  - high resolution - 10s or 30s only

CloudWatch Logs
- batch exporter to s3
- stream to elasticsearch
- tail using AWS CLI
- can encrypt log group
- retention setting
  - 1 day to 10years / 120 months
- export to s3
- subscription filter
  - to elasticsearch
  - to lambda
- metric filter
  - to alarm

Agents
- Logs Agent
  - logs to cloudwatch
- Unified Agent
  - system metrics - RAM, etc
  - logs
  - configure using SSm parameter store