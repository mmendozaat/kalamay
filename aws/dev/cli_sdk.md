when you encounter an error in cli call
- it returns an encoded failure message
- to read message/get detail of message
  - `aws sts decode-authorization-message --encoded-message <message>`
    - requires policy action allow `sts:DecodeAuthorizationMessage`
    - return a message that is in json

instance metadata
- http://169.254.169.254/latest/meta-data/ - trail with slash

profiles
- `aws configure --profile <profilename>`
- ~/.aws/credentials
```
[default]
...
[profile profilename]
...
```

CLI with MFA
- enable MFA
- `aws sts get-session-token --serial-number <mfa device arn> --token-code <mfa code>`
  - returns credentials
```
{
  "Credentials": {
    "AccessKeyId": ...,
    "SecretAccessKey": ...,
    "SessionToken": ...,
    "Expiration": ...
  }
}
```
- add `aws_session_token = <token>` in `~/.aws/credentials`

SDK Trivia
- default region when using SDK `us-east-1`

AWS Limits (Quotas)
- API Rate Limits
  - ec2:DescribeInstances - 100 calls/s
  - s3:GetObject - 5500 GET/s per prefix
  - Intermittent Errors - exponential backoff
  - Consistent Errors - request increase limits
  - `ThrottlingException` - exponential backoff
    - exponential backoff
      - 1s, 2s. 4s, 8s, 16s - double every iteration
      - built-in in SDK API calls
- Service Quotas (Service Limits)
  - on-demand standard instances - 1152 vCPU
  - service limit increase - open a ticket
  - service quota increase - use Service Quotas API

CLI Credentials order
- command line options
- environment variables
  - `AWS_ACCESS_KEY_ID`
  - `AWS_SECRET_ACCESS_KEY`
  - `AWS_SESSION_TOKEN`
- credentials file
- container credentials - ECS
- instance profile credentials - EC2

SDK Credentials Order
- Java
  - java system properties
  - environment variables
  - credentials file
  - ECS container credentials
  - EC2 instance profile credentials

Signing AWS HTTP API calls
- using SDK/CLI - signed
- otherwise
  - sign using Signature v4 (SigV4)
  - HTTP Header
    - Authorization
    - Credential
    - Signature
  - Query String option (ex. pre-signed S3 URLs)
    - X-Amz-Algorithm
    - X-Amz-Credential
    - X-Amz-Date
    - X-Amz-Signature