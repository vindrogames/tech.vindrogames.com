---
title: "From 0 to www using AWS and Github Actions - Part 1"
date: 2022-01-25T16:47:30+01:00
draft: true
---

# Introduction

On this series of articles we will see how we can host our own static content USING:
* Amazon Web Services (AWS) free tier, 
* Infrastructure as Code (IaC), 
* automatic deployments using Continous Integration / Continous Delivery (CI/CD) with Github Actions.

## Pre-Requisites

For the first part we need to have at least:  
* AWS Account
* IAM user with AdministratorAccess policy attached
* The user credentials (acccess key and secret access key) so we can execute commands via CLI
* CLI Linux console (to execute commands) with AWS SDK installed

## Bootstrap the account using CloudFormation

To bootstrap the first configurations needed we will use CloudFormation  
Change "ACCOUNTNUMBER" with the account number or any other name, just remember that the s3 bucket that will be created need to have a globally unique name.  
This will create the necesary infrastructure to use Terraform as our IaC provider:
* S3 Bucket
* DynamoDB table
```yaml
Resources:
  tfStateBucket:
    Type: AWS::S3::Bucket
    Properties:      
      BucketName: tfstate-ACCOUNTNUMBER
  tfStateDynamoDB:
    Type: AWS::DynamoDB::Table
    Properties: 
      AttributeDefinitions: 
        - 
          AttributeName: "LockID"
          AttributeType: "S"
      BillingMode: "PAY_PER_REQUEST"
      KeySchema: 
        - 
          AttributeName: "LockID"
          KeyType: "HASH"
      TableName: "aws-locks"
      Tags: 
        - Key: BuiltBy
          Value: CloudFormation
```

