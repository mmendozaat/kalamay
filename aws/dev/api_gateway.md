### Api gateway
- supports websockets
- api versioning
- different environments - dev, test, prod
- handle authorization, request throttling, api keys
- swagger/open api support
- generate SDK/API specifications
- cache API responses
- expose any aws api through gateway

**Endpoint Types**
- edge-optiimized
  - global
  - api gateway in 1 region
  - requests routed through cloudfront edge locations
- regional
- private
  - within VPC only using interface VPC endpoint (ENI)

**API types**
- HTTP API
- WebSocket API
- REST API
- REST API - private

**Lambda Alias/stage variables**
- api gateway can point to lambda alias
- lambda alias can point to more than 1 lambda function
  - 95% to v1, 5% to v2
- create lambda alias for different versions of lambda function
- method point to lambda function defined as  `function:${stageVariables.lambdaAlias}`
- create stage and add stage variable `lambdaAlias = alias`
- stage url: `https://endpoint/stage-name`

**Canary deployments**
- blue/green deployments
- prod = 95% of traffic
- prod canary = 5% of traffic
- metrics and logs are separate
- override stage variables for canary
- create canary (alternate version)
  - deploy to canary
  - promote canary

**Integration Types**
- MOCK - no backend
- HTTP/AWS - Lambda + AWS services
  - configure both integration request and integration response
  - setup request/response mapping - mapping templates
  - VTL - velocity Template Language
  - modify request/response
  - use case
    - soap api backend
      - api gateway uses mapping templates to convert json restful calls to xml to pass to soap
      - soap api response in xml is converted to json by api gateway
    - modify query-string parameters
      - ex. use mapping templates to change variable names
- AWS_PROXY - lambda proxy
  - lambda function responsible for request/response
  - api gateway is proxy
- HTTP_PROXY
  - request/response passed to/from backend
  - api gateway is proxy

**Swagger/Open API spec**
- common way of defining REST APIs
- yaml or json
- can import existing Swagger API to API gateway
- can export current API to Swagger
- using Swagger, we can generate SDK for applications

**Gateway Cache**
- defined per stage
  - override per method
- TTL
  - default: 300s
  - range: 0s - 3600s
- can be encrypted
- define capacity
  - range: 0.5GB to 237GB

**Cache Invalidation**
- per key
- flush entire cache in UI
- use header
  - Cache-Control: max-age=0
    - requires proper IAM authorization
      - "Action": ["execute-api:InvalidateCache"]
    - choose "Require authorization" in UI
    - default: anyone can invalidate cache

**Settings**
- can be modified on per resource-method level

**Usage Plans and API Keys**
- Usage Plans
  - who can access
  - how much and how fast
  - identify clients with API keys
  - meter clients
  - configure throttling and quota limits on clients
- API keys
  - customer keys - string
  - throttling limits applied to API keys
  - quota limits - overall number of maximum requests
- configure
  - configure methods to require api key
  - deploy api to stages
  - generate or import api keys to be distributed
  - create a usage plan with throttle and quota limits
  - associate api key and stages to usage plan
- request - provide api key in `x-api-key`

**Logging and Tracing**
- cloudwatch logs
  - enable at stage level
  - metrics
    - by stage
    - CacheHitCount, CacheMissCount
    - Count - # of requests per period
    - IntegrationLatency - time between api gateway send request to backend to time it receives a response
    - Latency -  time between api receive request to time it returns response
    - 4xx - client-side errors
      - 400 - bad request
      - 403 - access denied, waf filtered
      - 429 - quota exceeded, throttle
    - 5xx - server-side errors
      - 502 - bad gateway
      - 503 - service unavailable
      - 504 - integration failure
        - ex. endpoint request timed-out
- x-ray
  - tracing

**Account limits**
- 10,000 requests/s across all APIs
  - can increase upon request
- throttling - `429 Too Many Requests` error
- set Stage limit and Method limits
- or define usage plans to throttle per customer

**CORS**
- receive api calls from another domain
- cross-origin resource sharing
- pre-flight request OPTIONS/ method
  - specify origin
- receive pre-flight response with headers
  - access-control-allow-methods
  - access-control-allow-headers
  - access-control-allow-origin
- request from origin
- can also enable on console
- does not work for lambda_proxy
  - proxy does not allow modification of request/response

#### security - authentication/authorization

**IAM permissions**
- authentication - via IAM
- authorization - via IAM policy
- sig v4
  - iam credentials in headers
- resource policies
  - allow for cross-account access
  - allow specific IP
  - allow VPC endpoint

#### authorizers

**cognito user pools**
- manages user lifecycle
- token expires automatically
- authentication = cognito user pools
- authorization = api gateway methods

**lambda authorizer**
- or custom authorizer
- token-based authorizer - jwt or oauth - bearer token
- authentication = custom/3rd party
- authorization = lambda function
- request with bearer token or request params
- lambda authorizer verifies token
  - return iam principal/policy to api gateway
- api gateway 
  - caches iam policy

**HTTP API vs REST API vs WebSockets**
- HTTP API
  - low cost
  - proxy integrations
    - no data mapping
  - no usage plans/api keys
  - lambda and HTTP only, other AWS services - no
- REST API
  - all features
    - except native openid connect/oauth 2
- WebSockets
  - 2 way interactive communication between browser and server
  - server can push to client
  - stateful application
  - real-time platforms
    - chat
    - multi-player games
    - financial trading
  - works with 
    - lambda
    - dynamo db
    - or http endpoints

authorizers | HTTP API | REST API
--|--|--
AWS Lambda ||x
IAM||x
Amazon Cognito|x*|x
Native OpenID Connect/OAuth 2.0|x||
