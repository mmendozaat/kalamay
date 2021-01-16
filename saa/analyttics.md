#### Kinesis

1. Data Streams
- data in shards
- shard - 1MB/s in, 2MB/s out
- shard - 1000 PUT/s
- default limit of 500 shards - request to increase
- data retention default 24 hours, max 7 days
- resharding - split or merge
- pay per shard
- manually add shards
- shard by partition key
- record - partition key, sequence number, data blob
2. Data Analytics
- SQL queries on streaming data
- ingest from streams and firehose
- output to s3, redshift, elasticsearch, streams
3. Data Firehouse
- store into S3 or redshift
- stream from producer - elastic sharding
- can go to lambda, s3, redshift, elasticsearch or splunk
- fully managed
- 60s latency
- sources - kinesis stream, cloudwatch logs, etc
- buffer before write - by size or interval
- compression - snappy, zip, gzip
- encryption - on off - define kms
- allow lambda transform
- allow file conversion via glue
- replicates data to 3 AZs

Consumers
- use KCL (kinesis consumer library)

Producers
- Kinesis Streams API
- Kinesis Producer Library
- Kinesis Agent

Kinesis|SQS|SNS
--|--|--
pull|pull|push to subscribers
concurrent consumer access|multiple consumers|integrates with SQS for fan-out
-|-|pub-sub
ordered at shard level|unordered or FIFO|up to 10k topics
no limit on consumers||up to 10M subscribers
consume data later|delay message, explicit delete message|data does not persist
provisioned throughput|dynamic scaling|


```
$ aws kinesis create-stream --stream-name ... --shard-count ...
$ aws kinesis describe-stream --stream-name ...
$ aws kinesis put-record --stream-name ... --partition-key ... --data ...

$ aws lambda create-event-source-mapping --function-name ... --event-source <arn> --batch-size ... --starting-position LATEST
$ aws lambda delete-event-source-mapping --uuid ...
$ aws lambda list-event-source-mappings
```

#### EMR

- Hadoop
- Spark, HBAse, Presto, Flink

**step execution**
- create cluster
- execue steps
- cluster terminated

components
1. master
2. core node
3. task node (optional - for additional compute)

* + EMR role
* + EC2 instance role
*can resize cluster
*EMR in VPC private subnet - create s3 endpoints or NAT to access public AWS service endpoints

EMR notebooks

#### Athena

- query S3 via SQL
- serverless
- uses AWS Glue Data Catalog to store schema info
- Presto - CSV, JSON, ORC, Parquet, Avro

#### Glue

- fully-managed ETL
- data discovery via Data Catalog
- apache spark
- works with S3, redshift, rds, ec2

streams to lambda ?
streams to ec2 ?
firehose to lambda ?
