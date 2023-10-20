# Scaling Up and Down for RDS instance using Lambda Function

This guideline is providing how to scale up and down (resizing) RDS instance size automatically using Lambda function.

## Steps

- [Setup the required IAM policy](#SetUp_IAMpolicy)
- [Setup the required IAM roles and attach policy](#IAMroles)
- [Create an AWS Lambda Function](#Create_an_Lambda_Function)
- [Add Inline Policy](#Inline_Policy)
- [Function Code for Lambda](#Function_code_for_lambda)
- [Testing the Lambda Function](#Function_code_for_lambda)

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
![alt text](https://github.com/yelinaung-openlab/AWS-RDS-Aurora-Lab/blob/main/RDS-AutoScaleUpDown-with-Lambda/02-IAMRole.png?raw=true)

> Screenshot: 03-IAMRole.png
![alt text](https://github.com/yelinaung-openlab/AWS-RDS-Aurora-Lab/blob/main/RDS-AutoScaleUpDown-with-Lambda/03-IAMRole.png?raw=true)

> Screenshot: 04-IAMRole.png
![alt text](https://github.com/yelinaung-openlab/AWS-RDS-Aurora-Lab/blob/main/RDS-AutoScaleUpDown-with-Lambda/04-IAMRole.png?raw=true)

## Create an AWS Lambda Function

Will create AWS Lambda function to modify RDS instance class. 
<br>
First, have to select **Author from scratch** => **Function Name** => **Runtime(Python 3.7 or 3.8)** => **Existing Role "RDS_autoscaling_role"**.

> Screenshot: 05-LambdaFunction.png
![alt text](https://github.com/yelinaung-openlab/AWS-RDS-Aurora-Lab/blob/main/RDS-AutoScaleUpDown-with-Lambda/05-LambdaFunction.png?raw=true)

> Screenshot: 06-LambdaFunction.png
![alt text](https://github.com/yelinaung-openlab/AWS-RDS-Aurora-Lab/blob/main/RDS-AutoScaleUpDown-with-Lambda/06-LambdaFunction.png?raw=true)
<br>

## Add Inline Policy

Open a new tab for the IAM role and edit the existing role **RDS_autoscaling_role**. In the summmary page, click on **Add Inline Policy**.

> Screenshot: 07-IAMinlinepolicy.png
![alt text]
<br>
In the Inline policy editor, paste the following **JSON**. 
<br>
```bash
{   
   "Version": "2012-10-17",
   "Statement": [
{
      "Effect": "Allow",
      "Action": "lambda:GetFunctionConfiguration",
      "Resource": "arn:aws:lambda:ap-southeast-1:813444422663:function:RDS_autoscaling_function"
      }
   ]
}
````
**NOTE:**
Need to replace with your existing Lambda Function ARN.
<br><br>
Lambda ARN format: <br>
**arn:aws:lambda:your_aws_region:your_aws_account_id:function:your_lambda_function_name**

<br>
> Screenshot: 08-IAMinlinepolicy.png
![alt text]

<br>
> Screenshot: 09-IAMinlinepolicy.png
![alt text]

