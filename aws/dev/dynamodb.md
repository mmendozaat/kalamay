### NoSQL
- no joins
- no aggregations

### DynamoDB
- fully managed
- HA replication - 3 AZs
- distributed
- auto-scaling
- millions requests/s
- trillions of rows
- 100s of TB of storage
- event-driven programming - DynamoDB streams
- must have primary key
  - can only be string, binary or number
  - partition key only (hash key)
  - or partition key (hash key) + sort key (range key)

- rows ~ items
  - max 400 kb
- columns ~ attributes
  - nested
  - not fixed

data types
- scalar
  - string
  - number
  - boolean
  - binary
  - null
- document types
  - list
  - map
- set types
  - string set
  - number set
  - binary set

Create Table
- Default Settings
```
    No secondary indexes.
    Provisioned capacity set to 5 reads and 5 writes.
    Basic alarms with 80% upper threshold using SNS topic "dynamodb".
    Encryption at Rest with DEFAULT encryption type.
```
- Default alarms
  - ReadCapacityUnitsLimit
  - WriteCapacityUnitsLimit

*new feature* 
- kinesis data streams for dynamo db
  - item level changes as kinesis data stream
- encrypted by default

Provisioned Throughput
- can be exceeded temporarily using "burst credit"
- if "burst credit" used up, error "ProvisionedThroughputExceededException"
  - do exponential back-off retry

WCU - 1 write/s up to 1kb per item
- calculate WCU

Read consistency
- default - eventually consistent reads
  - to force strongly consistent
    - provide parameter - ConsistentRead=True in GetItem query or scan

RCU
- 1 strongly consistent read/s
- or 2 eventually consistent reads/s
- item up to 4kb

Partitions
- WCU and RCU - spread evenly across partitions
- table divided into partitions
  - partition keys are hashed to determine partition
- calculate number of partitions
  - capacity: (total rcu/3000) + (total wcu / 1000)
    - max 3000 RCUs/partition
    - max 1000 WCUs/partition
  - size: total size/10GB
    - max 10GB per partition
  - total partitions = ceiling(max(capacity, size))

Throttling
- exceed RCU or WCU - ProvisionedThroughputExceededException
  - hot keys - one partition key is read too many times 
  - hot partitions
  - very large items
  - soln:
    - exponential back-off
    - distribute partition keys
    - if RCU issue, use DynamoDB Accelerator (DAX)

Write types
- standard
- transactional - ??

Auto Scaling
- define
  - WCU and RCU settings
    - target utilization %
    - minimum provisioned capacity
    - maximum provisioned capacity
    - toggle - apply same settings to global secondary indexes

Read-write capacity mode
- provisioned (free-tier eligible)
- on-demand - ??

#### Dynamo DB API

**Writing Data**
- PutItem
  - create or replace
  - uses WCU
- UpdateItem
  - partial update of attributes
  - can use Atomic Counters and increase them
- Conditional Writes
  - write or update only if condition
  - to deal with concurrency
  - no performance impact

  **Deleting Data**
  - DeleteItem
    - conditional delete
  - DeleteTable
    - quicker than DeleteItem on all items

  **Batch Writes**
  - BatchWriteItem
    - up to 25 PutItem and/or DeleteItem in 1 call
    - no UpdateItem
    - up to 16MB written
    - up to 400kb data per item - max item/row size
    - use it to reduce number of api calls done
    - done in parallel
    - part of batch can fail
      - retry failed items using exponential back-off

**Read Data**
- GetItem
  - by primary key
    - hash or hash-range
  - default - eventually consistent
  - ProjectionExpression
    - include only certain attributes
- BatchGetItem
  - done in parallel
  - up to 100 items
  - up to 16MB

**Query**
- by partition key (=)
  - optional sort key (= < > <= >= between or begin)
- FilterExpression
  - happens on client side
  - all data is returned
- return 
  - up to 1MB data
  - up to "Limit"
- can do pagination
- query 
  - table
  - local secondary index
  - global secondary index

**Scan**
- scans the entire table
  - then filter
- returns up to 1MB of data
  - use pagination to read more
  - use limit
- parallel scans
  - multiple partitions at the same time
- use ProjectionExpression (limit attributes) + FilterExpression (limit rows/items)
  - same RCU consumed
  - happens on client side

Notes:
ALL_PROJECTED_ATTRIBUTES can be used only when Querying using an IndexName

#### Local Seconday Indexes (LSI)
- create index with alternate range/sort key within partition/hash key
- up to 5 LSI per table
- sort key - 1 scalar attribute
  - scalar - string, number, binary
- define LSI at creation time

#### Global Secondary Index (GSI)
- define new partition key + sort key (optional)
- like a "new" table
- project attributes
  - original table keys are always projected
  - options
    - KEYS_ONLY
    - INCLUDE <attribute>
    - ALL
- must define WCU/RCU for index
- can add or change GSI

#### Throttling
- if GSI writes are throttled, main table is throttled
- LSI - uses WCU and RCU of main table

#### Concurrency Model
- Conditional update/delete
- default - optimistic locking/concurrency

### Dynamo DB Accelerator (DAX)
- seamless cache
- no application rewrite
- write through to dynamo DB
- microsecond latency reads
- solves hot key problem
- default cache TTL - 5 minutes
- up to 10 nodes in cluster
- multi-AZ (3 nodes minimum for production)
- secure - encryption, VPC, cloudtrail, etc
- encrypted by default, can choose to not encrypt
- elasticache can still be used with dynamo db
  - different use case
- IAM service role for DAX cluster
  - This IAM service role and attached policy specifies the DynamoDB tables that the DAX cluster has access to and the DynamoDB APIs that the DAX cluster can execute.
- security group
  - A security group acts as a firewall that controls network access to your DAX cluster. To access the DAX cluster from your application, you must enable inbound access on port 8111 for this security group.
- other settings
  - cluster description
  - parameter group

**default settings**
```
    Automatic cache eviction enabled with TTL of 5 minutes
    No preference set for availability zones
    No preference set for maintenance windows
    Notifications disabled
```

#### Dynamo DB Streams
- stream of changes in dynamo db (CRUD - create update delete)
  - read stream - lambda, ec2
    - react to changes
    - analytics
    - create derivative tables/views
    - insert into elasticsearch, dynamo db, whatever
- cross-region replication possible
- 24 hours data retention
- choose data to be written in stream
  - KEYS_ONLY
  - NEW_IMAGE
  - OLD_IMAGE
  - NEW_AND_OLD_IMAGES
- made of shards
  - managed by AWS
- lambda
  - dynamo db stream - event-source-mapping
  - polls streams, gets batch in return
  - invoked with event batch
  - invoked synchronously

**lambda trigger**
- choose dynamo db table
  - go to triggers
    - select existing lambda
    - or create new lambda function
      - takes you to lambda console
        - configure blueprint dynamodb-process-stream
- define
  - batch size/window
  - starting position
    - trim horizon
    - latest

Notes:
- ShardIteratorType - API
  - denotes starting point for reading DynamoDB streams
  - AT_SEQUENCE_NUMBER
  - AFTER_SEQUENCE_NUMBER
  - TRIM_HORIZON
    - read from last (untrimmed) stream record (oldest record in shard)
    - trim - remove stream records that exceed the 24 hour retention limit
  - LATEST
    - read from most recent stream record in the shard

#### TTL - time to live
- delete items from table after TTL
- deletion happens within 48 hours of expiration
- deleted items also go to streams
- enabled per row
- does not use WCU/RCU
- background task within DynamoDB itself
- use cases
  - regulatory
  - reduce storage
- to use
  - create an attribute with an expiration timestamp in unix epoch timestamp
  - manage TTL - identify the attribute that defines the TTL

### CLI
- `--projection-expression` - attributes to retrieve
- `--filter-expression` - filter results
- optimization 
  - `--page-size` - each call will get less data - less timetouts
- pagination
  - `--max-items` - max number of results returned by CLI
    - return *NextToken*
    - pass received token as `--start-token`, to get next set of results

example
```
$ aws dynamodb scan --table-name UserPosts --max-items 1
$ aws dynamodb scan --table-name UserPosts --max-items 1 --starting-token "eyJFeGNs"
$ aws dynamodb scan --table-name UserPosts --filter-expression "user_id = :u" --expression-attribute-values '{":u": {"N":"1"}}'
$ aws dynamodb scan --table-name UserPosts --projection-expression "user_id, content"
```

#### Transactions
- all or nothing
- write modes
  - standard
  - transactional
- read modes
  - eventual consistency
  - strong consistency
  - transactional
- consume 2x WCU / RCU

**Use DynamoDB as session state cache**
- same use case for elasticache, both are key-value stores
  - elasticache
    - in-memory
  - dynamodb
    - serverless

#### Write Sharding
- if no suitable candidate for partition key
  - add a random suffix to candidate key that is not unique

#### Write Types
- Concurrent Writes
- conditional writes
- atomic writes - ??
- batch writes 

#### Large Objects Pattern
- write data in s3
- write metadata referring to s3 object in dynamo db

#### indexing s3 objects metadata
- write s3
- trigger lambda
- write s3 object metadata into dynamo db

#### operations
- table cleanup
  - drop + recreate
- copy a table
  - use AWS Data Pipeline - uses EMR
    - read from dynamo db - write to s3
    - then
    - read from s3 - write to dynamo db
  - backup and restore

#### security, etc
- VPC endpoints for dynamo db
- IAM policies - access control
- encryption at rest - KMS
- encryption in transit - SSL/TLS
- point-in-time restore - no performance impact
- global tables
  - multi-region
  - fully replicated
  - high performance
- AWS DMS - data migration service
  - migrate from mongo, oracle, mysql, s3, etc - ??
- can launch local dynamo db for development
