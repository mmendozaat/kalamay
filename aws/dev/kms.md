### KMS

- Symmetric
  - same key to encrypt and decrypt
  - AES 256
  - KMS
- Assymetric
  - key pairs - public(encrypt)/private(decrypt)
  - RSA, ECC
  - sign/verify
Key
- create
- enable
- disable
- rotate
- audit usage
- can encrypt up to 4kb data per call
  - if > 4kb, use envelope encryption
    - `GenerateDataKey`
      - get plaintext data key (DEK) + encrypted copy
    - `GenerateDataKeyWithoutPlaintext`
      - returns encrypted DEK only
    - `GenerateRandom`
      - generate random byte string
- bound to a region
  - copy snapshot to different region
    - reencrypt with key in region

`aws kms encrypt --key-id ... --plaintext fileb://... --output text --query CiphertextBlob`
- output is a encrypted file that is base64 encoded

`aws kms decrypt --ciphertext-blob fileb://... --output text --query Plaintext`
- outputs the plaintext base64 encoded

Encryption SDK
- feature
  - data key caching
    - reuse data keys
    - use LocalCryptoMaterialsCache
      - max age, bytes or number of messages

S3
- force SSL
  - deny if "aws:SecureTransport": "false"
  - if allow on "aws:SecureTransport": "true"
    - allow anonymous GetObject calls using SSL

### SSM Parameter Store - Systems Manager
- SDK, API
- serverless
- version tracking
- notification via CloudWatch events
- integrates with cloudFormation
- `GetParameters`, `GetParametersByPath`
- advanced store
  - add TTL to parameter
- name as path
- no rotation

`aws ssm get-parameters --names </path/name1> </path/name2> --with-decryption`

`aws ssm get-parameters-by-path --path </path> --recursive`

python:
```
ssm = boto3.client("ssm")
password = ssm.get_parameters(Names=["/path/name1"], WithDecryption=True)
```

### Secrets Manager
- can force rotation of secrets
  - rotation done by lambda function - custom
- encryption with KMS
- RDS/Redshift/DocumentDB integeration
- automatic generation
- cloudFormation integration

Encrypt CloudWatch log group
- cli only
`aws logs associate-kms-key --log-group-name ... --kms-key-id ...`