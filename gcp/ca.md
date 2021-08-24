Big able
- managed NoSQL 
- Petabyte-scale
- persistent hash table
- HBase API
- Encrypted at-rest, in-flight
- Powers google search, maps and gmail
- wide column
- 10MB/cell, 100MB/row

Cloud Datastore
- horizontally scalable NoSQL
- Terabyte-scale
- for application backends (App Engine/Compute Engine)
- supports SQL
- supports Transactions
- Free daily quota
- 1MB/entity

Cloud SQL
- managed RDBMS
- MySQL, Postgres

Cloud Spanner
- managed RDBMS
- horizaontally scalable
- petabytes size
- transactions
- 10MB/row

BigTable >~ Cloud Datastore
Cloud Spanner >~ Cloud SQL

Cloud Dataproc
- managed Hadoop
- Spark, Hive and Pig
- billed by the second, min 1 minute
- Manage cluster-size yourself

Cloud Dataflow
- Clusters are sized for you
- optimized partitioning
- don't worry about hot keys

BigQuery
- fully managed data warehouse
- separate storage and compute
- automatically drops storage cost > 90 days

PubSub
- at least once delivery
- 1M messages/s
- pairs well with Dataflow

Cloud Datalab
- jupyter notebooks
