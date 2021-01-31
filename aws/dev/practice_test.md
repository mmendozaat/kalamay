symmetric encryption
- same key to encrypt and decrypt

assymetric ecryption
- use a public key and a private key

EBS does not support assymetric KMS

envelope encryption
- encrypt the encryption key
- combine different encryption methods

http://169.254.169.254/latest/meta-data

Note that the IP address 169.254.169.254 is a link-local address and is valid only from the instance.

**learn**
- step functions
- cloudformation

beanstalk components
- ec2
- cloudwatch
- elb

step functions
- input is json
- output of state is json
  - can be input of another state
- input path > paramters - state > resultpath > outputpath

lambda throttling
- configure reserved concurrency
- use exponential backoff
- request quote increase - concurrent executions

opsworks
- managed chef and puppet

aws data pipeline
- moving data between services using emr

aws global accelerator
- improve performance of network traffic using aws global infrastructure instead of public internet

elasticache
- memcached 
  - does not support replication