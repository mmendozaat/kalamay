**Database**
- postgres
- mysql
- maria db
- oracle
- sql server
- aurora db

advantages
- managed
  - automated provisioning
  - os patching
  - continuous backups
  - restore to specific timestamp
  - monitoring
  - read replicas
  - multi-AZ (for DR)
  - maintenance windows for upgrades
  - scaling (horizontal or vertical)
  - EBS-backed storage (gp2 or io1)

backups
- enabled by default
- automated
  - daily - during maintenance window
  - transaction logs - backed up every 5 minutes
  - point-in-time restore - from up to 5 minutes ago
  - 7 days backup retention
    - up to 35 days
- db snapshots
  - manually triggered
  - unlimited retention

RDS Read Replicas
- up to 5 read replicas
  - within same AZ
  - cross AZ
  - or cross region
- async replication
  - eventually consistent
- read replica can be promoted to write master separate from original database
- cross az replication
  - cost $$$
- same az replication
  - no cost
- multi-az
  - disaster recovery
  - synchronous replication
  - one dns name
    - failover in case of loss of AZ
    - no manual intervention
    - standby becomes master
  - read replicas can have standby/multi-az for DR

**Encryption**
- at-rest encryption
  - encrypt master and read replicas with KMS - AES-256
  - encryption at create time
  - cannot encrypt replica if master is not encrypted
  - TDE (transparent data encryption) - oracle and sql server
- in-flight encryption
  - SSL certificates
    - postgres: rds.force_ssl=1 - in parameter groups
    - mysql: `GRANT USAGE ON *.* TO '<user>' REQUIRE SSL;`
- to encrypt unencrypted
  - create a snapshot
  - copy, enable encrypt
  - restore database from encrypted snapshot

**Security, Network and IAM**
- RDS are deployed in private subnet
- provide access via security groups
- IAM policies - who can manage RDS - through API
- use traditional username/password login
- can use IAM authentication - mysql and postgres only

**IAM Authentication**
- mysql and postgres only
- api call to GetAuthToken to RDS Service
  - receive token
  - pass token with SSL encryption to RDS
  - auth token has lifetime of 15 minutes
- benefits
  - SSL encrypted data exchange
  - use IAM to centrally manage users
  - leverage IAM roles and EC2 instance profiles for easy integration

**Security Responsibility**
- you
  - inbound rules in security group
  - user creation and permissions - in db or IAM
  - create database with or without public access
  - configure SSL in db via parameter groups
- aws
  - no ssh
  - no manual patching, db or os
  - no way to audit underlying instance

### Aurora
- compatible with Postgres and MySQL
- 5x performance improvement over MySQL in RDS
- 3x performance improvement over Postgres in RDS
- auto increase storage, increments of 10GB, up to 64 TB
- up to 15 replicas
  - mysql - up to 5 replicas
  - faster replication process
- instantaneous failover - HA native
- costs 20% more

**High Availability and Read Scaling**
- 6 copies of data across 3 AZs
  - 4 copies needed for writes
  - 3 copies needed for reads
- self healing with peer-to-peer replication
- storage is striped across 100s of volumes
- striped shared volume
  - replication
  - self-healting
  - auto-expanding volumes
- 1 master - up to 15 replicas, can auto-scale read replicas
- automated failover in less than 30s
- cross-region replication

**Cluster**
- writer endpoint 
  - points to single master
- reader endpoint 
  - points all replicas
  - load balancer to read from single replica

**Features**
- automatic failover
- backup and recovery
- isolation and security
- industry compliance
- push-button scaling
- automated patching with 0 downtime
- advanced monitoring
- routine maintenance
- backtrack - point-in-time recovery 

**Serverless**
- pay per second

**global aurora**
- cross-region read replicas
  - DR
- global database
  - 1 primary region (r/w)
  - up to 5 secondary regions (read only)
    - less than 1s lag
  - up to 16 read replicas per secondary
  - promote region to master
    - RTO < 1 minute

**auto scaling replicas**
- target metric
  - average cpu utilization of replicas
  - average connections of replicas

### ElastiCache
- in-memory database
- managed Redis or Memcached
- Write scaling using sharding
- Read scaling using Read Replicas
- Multi-AZ with Failover
- AWS managed
  - os maintenance
  - patching
  - optimizations
  - setup/configuration
  - monitoring
  - failure recovery
  - backups
- modify application to use elasticache
  - query
    - read cache
      - cache hit, retrieve data from cache
      - cache miss, retrieve data from database
        - write read data to cache
- as user session store
- not encrypted by default
  - enable encryption at rest
  - enable encryption in transit

#### Redis
- multi-AZ with auto-failover
- read replicas
- AOF persistence
  - data persists across restarts
- backup and restore features
- cluster mode possible
- in-memory cache and message broker
- if encryption in transit
  - use redis auth - specify a token
  - or use group acl

#### Memcached
- in-memory object caching
- distributed
- multi-node for partitioning / sharding
- non-persistent
- no backup and restore
- multi-threaded architecture

#### Caching implementation considerations
- is data safe to cache?
  - data can go out of date
- is caching effective for data?
  - pattern - data changing slowly, few keys frequently needed
  - anti-pattern - data changing rapidly, all large key space frequently needed
- is data structured well for caching?
  - key-value
  - aggregate results

**Lazy loading / cache-aside / lazy population**
- cache hit
- cache miss
  - query db
  - cache query results
- pros
  - only requested data is cached - lazy
  - node failures are not fatal
- cons
  - cache miss results in 3 round trips
    - cache query
    - db query
    - cache write
  - stale data
    - out of sync cache and db

**write through**
- write to db + write to cache all the time

**cache evictions and TTL**
- eviction
  - explicit cache delete
  - evicted due to memory full and pushd out of LRU
  - set an item TTL
- TTL
  - useful for
    - leaderboards
    - comments
    - activity streams
  - seconds to days
- too many evictions - scale up