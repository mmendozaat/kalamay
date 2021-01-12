EFS
- multiple instances can mount same volume
- linux only
- formatted outside of instance

NVMe
- instance storage
- ephemeral

SSD
- burst 3000 IOPS
- baseline 3 IOPS / GB

Provisioned IOPS (SSD)
- burst up to 20000 IOPS
- provision up to 50 IOPS / GB
- min/max = 100/64000

Magnetic HDD
- standard volumes
- average 100 IOPS
- burst to hundreds of IOPS

Volume Types
1. General Purpose SSD (gp1)
- 1GB-16TB
2. Provisioned IOPS SSD (io1)
- 4GB-16TB
3. Cold HDD (sc1)
- 500GB-16TB
4. Throughput Optimized HDD (st1)
- 500GB-16TB
5. Magnetic (standard)

*specify AZ during create

**mount a volume**
1. `$ lsblk`
2. `$ file -s /dev/xvdb`
3. `$ mkfs -t xfs /dev/xvdb`
4. `$ file -s /dev/xvdb`
5. `$ mkdir /data`
6. `$ mount /dev/xvdb /data`

*snapshots from one AZ can be used in volume in different AZ - same region
*can create ami from snapshot

*to delete an AMI, deregister

snapshots are incremental snapshots
- delete process allows deletion of earlier snapshots
- latter snapshots will be updated to contain all data

snapshot lifecycle manager
- create policy
- scheduled
- define # of snapshots to retain

*consistent snapshots require no writes on volume
- shutdown instance if root volume
- stop application if non-root volume

*can copy snapshot to different region, can encrypt
*can create volume from snapshot in another AZ, can encrypt
*can create AMI from snapshot - cannot move to another region/AZ/encrypt, encrypt during launch instance from ami
*can copy encrypted snapshot to different region, change key
*can create volume from snapshot from different AZ, change key

KMS key can be used my multiple accounts
- disable then schedule deletion - minimum 7 days, default 30

EBS-optimized instances
- dedicated EBS IOPS
- certain instance types, c, a, etc

**Important**
EBS-optimized instances
Raid 0 - striping
Raid 1 - mirroring
Raid 1+0

EFS
- ~ NAS
- linux only
- can connect to multiple instances
- can connect to instances in other AZ via peering
- can connect to instances in on-prem computers
- up to 1000s instances concurrently
- KMS only
- File Sync - up to 5x faster than linux tools

- auto backups by default
- lifecycle management - to infrequent access storage class 
- based on days since last access

EFS performance modes
1. general purpose
2. max i/o

EFS Throughput modes
1. bursting - scales with file system size
2. provisioned - fixed throughput

EFS Encryption
- at rest and in transit
- at rest enabled by default
- keys by KMS

*mount targets - endpoints
*associated IP address per endpoint

FSx
- managed file server
- fully-managed, 3rd party fs
- HPC, ML, electronic design automation
- for windows file server for windows-based applications
- for Lustre - for compute-intensive workloads
- over SMB protocol
- supports NTFS
- Active Directory support
- can read and write to/from S3
- multi or single AZ
- single subnet if single AZ
- Lustre minimum - 1200 GB

#### AWS Storage Gateway
1. File Gateway
- local file server on prem
- mountd using NFS (linux) or SMB (windows)
- files are in S3
- local file server serves as cache
- s3 limit - 5TB
2. Volume Gateway
- iSCSI protocol - block-level mode
a. cached volume mode
- cache on prem
- data on s3
b. stored volume mode
- data on-prem
- s3 contains data backups as snapshot
-*max 32 volumes @ 32TB each = total 1PT
3. Tape Gateway
- backup software + tape drive
- backup s3 data to on-prem
- moves to glacier and deep-archive after backup
