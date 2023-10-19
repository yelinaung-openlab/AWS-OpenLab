# Scaling Up and Down for RDS instance using Lambda Function

This guideline is providing how to scale up and down (resizing) RDS instance size automatically using Lambda function.

## Steps

- [Setup the required IAM policy](#SetUp_IAMpolicy)
- [Setup the required IAM roles and attach policy](#IAMroles)
- [Create an AWS Lambda Function](#Create_an_Lambda_Function)
- [Add Inline Policy](#Inline_Policy)
- [Function Code for Lambda](#Function_code_for_lambda)
- [TestingTime] (#Function_code_for_lambda)

## Setup IAM Policy

Firstly, create IAM Policy to gain access to RDS actions and AWS CloudWatch log events with the following policy.
> Screenshot: 01-IAMPolicy.png
![alt text](https://github.com/yelinaung-openlab/AWS-RDS-Aurora-Lab/blob/main/RDS-AutoScaleUpDown-with-Lambda/01-IAMPolicy.png?raw=true)

```bash
# IAM Policy for RDS and CloudWatch Logs with Json format

{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "Stmt1697699911419",
      "Action": [
        "rds:DescribeDBInstances",
        "rds:DescribeValidDBInstanceModifications",
        "rds:ModifyCurrentDBClusterCapacity",
        "rds:ModifyCustomDBEngineVersion",
        "rds:ModifyDBCluster",
        "rds:ModifyDBClusterEndpoint",
        "rds:ModifyDBClusterParameterGroup",
        "rds:ModifyDBClusterSnapshotAttribute",
        "rds:ModifyDBInstance",
        "rds:ModifyDBParameterGroup",
        "rds:ModifyDBProxy",
        "rds:ModifyDBProxyEndpoint",
        "rds:ModifyDBProxyTargetGroup",
        "rds:ModifyDBSnapshot",
        "rds:ModifyOptionGroup",
        "rds:ModifyRecommendation"
      ],
      "Effect": "Allow",
      "Resource": "*"
    },
    {
      "Sid": "Stmt1697700010854",
      "Action": [
        "logs:CreateLogGroup",
        "logs:CreateLogStream",
        "logs:PutLogEvents"
      ],
      "Effect": "Allow",
      "Resource": "*"
    }
  ]
}
```

## Setup IAM Roles
Create an IAM role and attach the previous policy which created.

> Screenshot: 02-IAMRole.png
> Screenshot: 03-IAMRole.png
> Screenshot: 04-IAMRole.png
