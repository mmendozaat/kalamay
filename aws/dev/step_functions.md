### Step Functions

- build serverless visual workflow
- max execution time = 1 year
- add human approval feature
- errors
  - retry
    - `IntervalSeconds`
    - `MaxAttempts`
    - `BackoffRate`
  - catch
    - `ErrorEquals`
    - `Next`

**standard vs express**
-|standard|express
--|--|--
max duration|1 year|5s
execution start rate| 2k/s|100k/s
state transition rate|4k/s|unlimited
pricing|per state transition|per execution
execution semantics|exactly-once workflow|at-least-once workflow

**Example**
```
{
  "Comment": "A Hello World example demonstrating various state types of the Amazon States Language",
  "StartAt": "Pass",
  "States": {
    "Pass": {
      "Comment": "A Pass state passes its input to its output, without performing work. Pass states are useful when constructing and debugging state machines.",
      "Type": "Pass",
      "Next": "Hello World example?"
    },
    "Hello World example?": {
      "Comment": "A Choice state adds branching logic to a state machine. Choice rules can implement 16 different comparison operators, and can be combined using And, Or, and Not",
      "Type": "Choice",
      "Choices": [
        {
          "Variable": "$.IsHelloWorldExample",
          "BooleanEquals": true,
          "Next": "Yes"
        },
        {
          "Variable": "$.IsHelloWorldExample",
          "BooleanEquals": false,
          "Next": "No"
        }
      ],
      "Default": "Yes"
    },
    "Yes": {
      "Type": "Pass",
      "Next": "Wait 3 sec"
    },
    "No": {
      "Type": "Fail",
      "Cause": "Not Hello World"
    },
    "Wait 3 sec": {
      "Comment": "A Wait state delays the state machine from continuing for a specified time.",
      "Type": "Wait",
      "Seconds": 3,
      "Next": "Parallel State"
    },
    "Parallel State": {
      "Comment": "A Parallel state can be used to create parallel branches of execution in your state machine.",
      "Type": "Parallel",
      "Next": "Hello World",
      "Branches": [
        {
          "StartAt": "Hello",
          "States": {
            "Hello": {
              "Type": "Pass",
              "End": true
            }
          }
        },
        {
          "StartAt": "World",
          "States": {
            "World": {
              "Type": "Pass",
              "End": true
            }
          }
        }
      ]
    },
    "Hello World": {
      "Type": "Pass",
      "End": true
    }
  }
}
```

### AppSync
- managed service that uses GraphQL
- combine multiple sources
  - NoSQL, relational, HTTP APIs
  - Dynamo DB, Aurora, ElasticSearch
  - custom sources using AWS Lambda
- real-time with WebSocket or MQTT on WebSocket
- for mobile apps: local data access & data sync
- upload a graphQL schema
- process
  - client issues GraphQL query
  - use dynamo DB resolver to get data
  - form GraphQL response in JSON - matches schema
- components
  - schema
  - resolver
    - dynamoDB
    - aurora
    - elasticSearch
    - lambda - custom
    - HTTP - i.e public HTTP API endpoints
- security
  - API_KEY
  - AWS_IAM - uses/roles/cross-account access
  - OPENID_CONNECT - openid connect provider/jwt (json web token)
  - AMAZON_COGNITO_USER_POOLS
- custom domain & HTTPS
  - cloudfront in front of AppSync