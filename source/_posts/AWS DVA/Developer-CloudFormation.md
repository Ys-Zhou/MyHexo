---
title: 'Developer: CloudFormation'
date: 2022-02-09 16:22:55
tags: AWS - Developer
---

# Templates

## Resources

```yaml
Resources:
  MyEC2Instance:
    Type: AWS::EC2::Instance
    Properties:
      ImageId: ami-a4c7edb2
      InstanceType: t2.micro
```

## Parameters

```yaml
Parameters:
  MyInstanceType:
    Type: String

Resources:
  MyEC2Instance:
    Type: AWS::EC2::Instance
    Properties:
      ImageId: ami-a4c7edb2
      InstanceType: !Ref MyInstanceType
```

- Pseudo Parameters
  - AWS::AccountId
  - AWS::NotificationARNs
  - AWS::NoValue
  - AWS::Region
  - AWS::StackId
  - AWS::StackName

## Mappings

```yaml
Mappings:
  MyRegionImageId:
    us-east-1:
      '32': ami-6411e20d
      '64': ami-7a11e213
    us-west-1:
      '32': ami-c9c7978c
      '64': ami-cfc7978a

Resources:
  MyEC2Instance:
    Type: AWS::EC2::Instance
    Properties:
      ImageId: !FindInMap 
        - MyRegionImageId
        - !Ref AWS::Region
        - '32'
      InstanceType: t2.micro
```

## Outputs

```yaml
# Stack 1
Resources:
  MyEC2Instance:
    Type: AWS::EC2::Instance
    Properties:
      ImageId: ami-a4c7edb2
      InstanceType: t2.micro

Outputs:
  Value: !Ref MyEC2Instance
  Export:
    Name: MyEC2InstanceOutput
```

```yaml
# Stack 2
Resources:
  Type: AWS::EC2::EIP
  Properties:
    InstanceId: !ImportValue MyEC2InstanceOutput
```

## Conditions

```yaml
Conditions:
  CreateEC2Instance: !Equals
    - !Ref AWS::Region
    - us-east-1

Resources:
  MyEC2Instance:
    Type: AWS::EC2::Instance
    Condition: CreateEC2Instance
    Properties:
      ImageId: ami-a4c7edb2
      InstanceType: t2.micro
```

## Intrinsic Functions

### Fn::Ref
```yaml
# Ref the resource id
!Ref MyEC2Instance
```

### Fn::GetAtt
```yaml
# Get an attribute of a resource
!GetAtt MyEC2Instance.AvailabilityZone
```

### Fn::FindInMap
```yaml
# Find a value from a mapping
!FindInMap
  - MyRegionImageId
  - !Ref AWS::Region
  - '32'
```

### Fn::ImportValue
```yaml
# Get a exported value from other templates
!ImportValue MyEC2InstanceOutput
```

### Fn::Join
```yaml
# Join values into a string
!Join 
  - ::
  - - AWS
    - Region
```

### Fn::Sub
```yaml
# substitute
!Sub 
  - ${hostname}.${domain}
  - hostname: aws
    domain: amazon.com
```

### Condition Functions
- Fn::And
- Fn::Equals
- Fn::If
- Fn::Not
- Fn::Or

# Others

## Rollback

- If stack creation failed, it will rollback to the previous version automatically
  - Can be disabled
  - If no previous, it will be deleted

## Drift

- Detect if the resources created by CloudFormation is modified (configured different from the CloudFormation template)
