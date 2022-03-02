---
title: DOP - Configuration Management and Infrastructure as Code
date: 2022-02-27 23:23:35
tags:
---

# CloudFormation

## Every topics in "DVA - CloudFormation"

## User Data
- Use Fn::Base64 to pass the user data
- User data is logged in /var/log/cloud-init-output.log file

```yaml
Resources:
  MyEC2Instance:
    Type: AWS::EC2::Instance
    Properties:
      ImageId: ami-a4c7edb2
      InstanceType: t2.micro
      UserData:
        Fn::Base64: |
          #!/bin/bash -xe
          yum update -y
          yum install -y httpd
          ...
```

## cfn-init
- Use AWS::CloudFormation::Init block in Metadata to create a initialization script
- Run the script in user data

## cfn-signal & WaitConditions
- Use cfn-signal to send failed/succeeded signal back to CloudFormation
- Define WaitCondition to block the processing until it receives a signal from cfn-signal

### Troubleshoot when signal was not received
- Ensure that the AMI has CloudFormation helper scripts installed
- Verify cfn-init & cfn-signal by checking cloud-init.log & cfn-init.log files
  - Must disable rollback on failure to access to the instances
- The instances must have access to the Internet (to communicate with CloudFormation)
  - Either in public subnet or in private subnet using NAT gateway / NAT instance

## cfn-init & cfn-signal sample
```yaml
Resources:
  MyEC2Instance:
    Type: AWS::EC2::Instance
    Properties:
      ImageId: ami-a4c7edb2
      InstanceType: t2.micro
      UserData:
        Fn::Base64: !Sub |
          #!/bin/bash -xe
          yum update -y aws-cfn-bootstrap
          /opt/aws/bin/cfn-init -s ${AWS::StackId} -r MyEC2Instance --region ${AWS::Region} || error_exit 'Failed in cf-init'
          /opt/aws/bin/cfn-signal -e $? --stack ${AWS::StackId} --resource MyWaitCondition --region ${AWS::Region}
    Metadata:
      AWS::CloudFormation::Init:
        config:
          packages:
            yum:
              httpd: []
          files:
            "/var/www/html/index.html":
              content: |
                <h1>Hello, cf-init</h1>
              mode: 000644
          commands:
            echo:
              command: |
                echo "Hello, cf-init"
          services:
            sysvinit:
              https:
                enabled: true
                ensureRunning: true
  MyWaitCondition:
    Type: AWS::CloudFormation::WaitCondition
    CreationPolicy:
      ResourceSignal:
        Timeout: PT1M
        Count: 1
```

## Rollbacks
- OnFailure=ROLLBACK (default)
- OnFailure=DO_NOTHING
- OnFailure=DELETE

## Nested stack
- Need CAPABILITY_AUTO_EXPAND capability to create nested stacks
- Best practice: never update / delete nested stacks, do actions on the parent stack

```yaml
Resources:
  MyNestedStack:
    Type: AWS::CloudFormation::Stack
    Properties:
      TemplateURL: https://s3.amazonaws.com/.../myNestedStack.template
      Parameters:
        param_1: value_1
        param_2: value_2
```

## Deletion policies sample
```yaml
Resources:
  MyEC2Instance:
    Type: AWS::EC2::Instance
    DeletionPolicy: Retain # here
    Properties:
      ImageId: ami-a4c7edb2
      InstanceType: t2.micro
  MyEBSVolume:
    Type: AWS::EC2::Volume
    DeletionPolicy: Snapshot # here
    Properties:
      Size: 1
      VolumeType: gp2
```

## Termination protection
- Enable termination protection to protect the stack to be deleted

## Referent parameters from SSM parameter store
- You can also use the public parameters from SMM Parameter Store such as AMI IDs

```yaml
Parameters:
  InstanceType:
    Type: AWS::SSM::Parameter::Value<String> # here
    Default: /EC2/InstanceType
  ImageId:
    Type: AWS::SSM::Parameter::Value<AWS::EC2::Image::Id> # here
    Default: /EC2/ImageId
```

## DependsOn
- Define resources creation orders

```yaml
Resources:
  MyEC2Instance: # created after MyRDSInstance
    Type: AWS::EC2::Instance
    Properties:
      ...
    DependsOn: MyRDSInstance
  MyRDSInstance: # created first
    Type: AWS::RDS::DBInstance
    Properties:
      ...
```

## Define Lambda Function in CloudFormation

### Inline
- No dependency
- Codes are up tp 4000 characters

```yml
Resources:
  myLambdaFunction:
    Type: AWS::Lambda::Function
    Properties:
      Runtime: python3.x
      Handler: index.handler
      Code:
        ZipFile: |
          def handler(event, context):
            return 'OK!'
```

### S3
```yml
Resources:
  myLambdaFunction:
    Type: AWS::Lambda::Function
    Properties:
      Runtime: python3.x
      Handler: index.handler
      Code:
        S3Bucket: myBucket
        S3Key: myLambdaFunction.zip
        S3ObjectVersion: myVersionString
```

## Custom Resources (Lambda)
- Lambda function will be invoked only when the resource is created / updated / deleted
- Invoke events
```json
{
  "RequestType": "Create/Update/Delete",
  "ResponseURL": "https://a-per-signed-S3-URL",
  "StackId": "...",
  "RequestId": "...",
  "ResourceType": "Custom::MyCustomResource",
  "LogicalResourceId": "MyCustom",
  "ResourceProperties": {
    "ServiceToken": "...",
    "BucketName": "..."
  }
}
```

- Definition sample
```yaml
Resources:
  MyS3Bucket:
    Type: AWS::S3::Bucket
  MyCustom:
    Type: Custom::MyCustomResource
    Properties:
      ServiceToken: "lambdaFunctionARN"
      BucketName: !Ref MyS3Bucket
```

## Stack Status Code UPDATE_ROLLBACK_FAILED
- Errors
  - Failed to receive the required number of signals
  - Changes to resource were made outside of CloudFormation
  - Insufficient permissions
  - Invalid security token
  - Limitation error
  - Resource did not stabilize
- Solutions
  - Continue Update Rollback after resolving the problems
  - Delete the stack
  - Contact AWS Support

## InsufficientCapabilitiesException
- You need provide CAPABILITY_IAM or CAPABILITY_NAMED_IAM

## cfn-hup
- Using cfn-hup, hup daemon will periodically detect template changes and perform actions
- In UserData block, execute
```sh
/opt/aws/bin/cfn-bup || error_exit 'Failed in cf-hup'
```
- In UserData block or in AWS::CloudFormation::Init block (when using cf-init), create /etc/cfn/cfn-hup.conf file
```ini
[main]
stack="stack ID"
region="region"
interval="interval in minutes" # default is 15
```
- And create /etc/cfn/hooks.d/\<hooks>.conf file
```ini
[cfn-auto-reloader-hook]
triggers=post.update
path=Resources.MyResource.Metadata.AWS::CloudFormation::Init
action=/opt/aws/bin/cfn-init -v --stack ${AWS::StackName} --resource MyResource --region ${AWS::Region}
```

## Stack policies
- Allow / deny actions on the stack

## Other known features
- ChangeSets
- Drift detection
