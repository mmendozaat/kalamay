### CloudFormation

- templates must be uploaded to s3

deploying
- manual
  - CloudFormation Designer
- automated
  - yaml
  - aws cli

Building Blocks
- resources - required
- parameters - template inputs
- mappings  - static variables
- outputs - what was created
- conditionals 
- metadata

Helpers
- references
- functions

yaml
- key-value
- nested objects
- arrays `-`
- multi-line strings `|`
- references - !Ref <name>

```
Resources:
  <Name>:
    Type: AWS::<aws-product-name>::<data-type-name>
    Properties: ...
```

- cannot create dynamic resources
  - everything must be declared
- not all aws services are supported
  - use AWS Lambda custom resources


```
Parameters:
  <name>:
    Description:
    Type: String
```

Types:
- String
- Number
- CommaDelimitedList
- List<Type>
- AWS Parameter - catch invalid values

Reference a parameter
- `Fn::Ref` or `!Ref`

Pseudo Parameters (metadata)
- enabled by default
- AWS::AccountId
- AWS::Region
- AWS::StackName
- AWS::NoValue
- use `!Ref "AWS::Region"`

Mappings
```
Mappings:
  <name>:
    <key>:
      <name>: <value>
```
- `Fn::FindInMap`
- ex. `!FindInMap [MapName, TopLevelKey, SecondLevelKey]`

Outputs
- can be imported/referenced to other stacks
  - must be imported first
  - `Fn::ImportValue`
- cannot delete stack that is referenced by other stacks
```
Outputs:
  <name>:
    ...
    Export:
      Name: <name>
```

In another stack,
```
Resources:
  <name>:
    ...
    Properties:
      ...: !ImportValue <name>      
```

Conditions
```
Conditions:
  <condition-name>: !Equals [ !Ref EnvType, prod]

Resources:
  <name>:
    ...
    Condition: <condition-name>
```
- `Fn::And`, `Fn::Equals`, `Fn::If`, `Fn::Not`, `Fn::Or`

Intrinsic Functions
- Fn::Ref
- Fn::GetAtt, `!GetAtt <resource>.<attribute>`, ex: `!GetAtt EC2Instance.AvailabilityZone
- Fn::FindInMap
- Fn::ImportValue
- Fn::Join ex. 'a:b:c' `!Join [":", [a, b, c]]`
- Fn::Sub - substitute

Rollbacks
- create failure
  - all or nothing
  - option to disable rollback
- update failure
  - rollback to previous

ChangeSets

Nested Stacks

Cross Stacks
- Export, ImportValue
- stacks have different lifecycles

Nested Stacjs
- reuse components

??

StackSets
- multiple accounts/regions
- need trusted accounts
- update set, update all stack instances across accounts and regions

