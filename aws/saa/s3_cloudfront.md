### S3
- http://s3.<region>.amazonaws.com/\<bucket\>/\<object\>
- http://\<bucket\>.s3.\<region\>.amazonaws.com/\<object\>
- REsT API

EBS
- Elastic Block Storage
- locally mounted HDD/SSD
- same AZ

EFS
- Elastic File System
- cannot issue block level commands/format
- already formatted
- NFS mount
- linux only
- FS map to different AZ
- mount on on-premise/client site via VPN

S3 gateway endpoint ?
- private connection from VPC to S3
1. create endpoint - VPC
2. choose from services, gateway or interface type
2. choose vpc
3. choose routetable
- creates entry in routetable

Buckets
- name is global
- region is defined

Read Storage Classes
- standard
- standard ia
- onezone
etc

Policies
1. identity-based
  a. iam role - inline policy
  b. iam user - inline policy
  c. iam group - standalone policy
2. resource-based
- policy attached to service/specific resource

Cross-account access:
- use trust relationship
- use sts:AssumeRole
- sts - security token service
- switch role in console

ACL - old style
- bucket and object level
- account level only or predefined group level
- authenticaated users
- all users
- log delivery group

Canonical ID
- set by default in ACL

ACLs vs Policies
a. object ACL is the only way to manage objects not owned by bucket owner
b. manage permission at object level
- limit 20k policy size
- object ACL limited to 100 grants
- option: bucket policy and then per object ACL

Multi-part upload
- from 100MB to 5GB objects
- pause and resume upload
- parallel upload
- upload object as it is being created
- done automatically with large files

S3 query string authentication/pre-signed url
- generate url manually or programmatically
- tedious if manually
- can expire

```
$ aws s3 presign s3://path_to_object --expires-in 60
```

60 is in seconds

Transfer acceleration
- http://bucket.s3-accelerate.amazonaws.com
- http://bucket.s3-accelerate.dualstack.amazonaws.com -> ipv6
- use above endpoints for upload
- endpoints are cloudfront edge locations 
- you pay only if transfer time is improved

*block all public access option overrides bucket policy

versioning
- disabled
- enable
- suspend - cannot disable once enabled
- can delete version
- but deleted file version is kept and marked deleted

*can have MFA delete

Cross-Region Replication - CRR
- asynchronous copy of objects across regions
- requires versioning
- define prefix
- source and destination should be different buckets
- requires IAM role with trust relationship that allows s3 service to sts:assumeRole

Lifecycle Management
1. transition action
- move objects to another storage class
- applicable to current and previous versions
2. expiration action
- expire objects (delete)

Encryption
1. S3-managed (SSE-S3)
- aes256
- key per object
- 1 master key
- encrypted in store only not in transit
2. KMS-managed (SSE-KMS)
- customer can generate master key
- encrypt/decrypt at store not in transit
3. SSE-C
- client-managed (key on client side)
- encrypt/decrypt in store not in transit
- no ui
- pass details in header
4. Client-side Encryption
- fully out of AWS
- send/receive encrypted data

Event notifications
- send to
a. SNS topic
b. SQS queue
c. Lambda function

Requester Pays
- data transfer and request charges
- does not support
- anonymous
- bittorrent
- soap requests

Server Access Logging
- best effort

Object Lock
- WORM - write once, read many
- permanent - ?

object lock retention modes
1. governance mode
- requires IAM permission to disable
2. compliance mode
- cannot be disabled by anyone including root account
- must expire

Legal hold - object lock
- indefinite lock
- disabled with proper IAM permission

S3 Select ?
- bucket and in glacier

### CloudFront

Regional Edge Cache
- bigger

Edge Locations
- main cache
- closer to users

Origins
1. s3 origin
2. custom origins
- s3 static website
- ALB

Distributions
1. Web
2. RTMP
- adobe flash media RTMP protocol
- play while downloading

*Cloudfront using origin access identity (OAI) to access bucket (via policy)
*OAI -restrict access to S3. access through cloudfront only
**invalidations - remove stuff from cache
***disable before delete distribution

cloudfront cache behaviors
- default
- custom
- define how cloudfront responds under specific requests/conditions

lambda@edge
- cloudfront triggers for lambda
- based on cloudfront behaviors

*max 5TB s3 object
*max upload in single PUT request 5GB
*eventual consistency for overwrite and deletes
*bittorrent support
*max 100 buckets per account

*byte-range fetches ?
