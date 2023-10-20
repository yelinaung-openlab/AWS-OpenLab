# Scaling Up and Down for RDS instance using Lambda Function

This guideline is providing how to scale up and down (resizing) RDS instance size automatically using Lambda function.

## Steps

- [Setup the required IAM policy](#SetUp_IAMpolicy)
- [Setup the required IAM roles and attach policy](#IAMroles)
- [Create an AWS Lambda Function](#Create_an_Lambda_Function)
- [Add Inline Policy](#Inline_Policy)
- [Function Code for Lambda](#Function_code_for_lambda)
- [Testing the Lambda Function](#Function_code_for_lambda)
- [Add the trigger for Lambda](#Add_the_trigger_for_lambda)

## Setup the required IAM Policy

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

## Setup the required IAM roles and attach policy
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
![alt text](https://github.com/yelinaung-openlab/AWS-RDS-Aurora-Lab/blob/main/RDS-AutoScaleUpDown-with-Lambda/07-IAMinlinepolicy.png?raw=true)
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
```

**NOTE:**
Need to replace with your existing Lambda Function ARN.
<br><br>
Lambda ARN format: <br>
**arn:aws:lambda:your_aws_region:your_aws_account_id:function:your_lambda_function_name**

<br>
> Screenshot: 08-IAMinlinepolicy.png

![alt text](https://github.com/yelinaung-openlab/AWS-RDS-Aurora-Lab/blob/main/RDS-AutoScaleUpDown-with-Lambda/08-IAMinlinepolicy.png?raw=true)

<br>
> Screenshot: 09-IAMinlinepolicy.png

![alt text](https://github.com/yelinaung-openlab/AWS-RDS-Aurora-Lab/blob/main/RDS-AutoScaleUpDown-with-Lambda/09-IAMinlinepolicy.png?raw=true)


## Function Code for Lambda

Scroll down and paste the Python code inside the editor. Need to select the python version 3.8.

> Screenshot: 10-LambdaFunctionCode.png

![alt text](https://github.com/yelinaung-openlab/AWS-RDS-Aurora-Lab/blob/main/RDS-AutoScaleUpDown-with-Lambda/10-LambdaFunctionCode.png?raw=true)

**Python Code**
<br>
We are using 2 environment variables:
- DBinstance
- DBinstanceClass

```bash
import sys
import botocore
import boto3
import json
from botocore.exceptions import ClientError
def lambda_handler(event, context):
    rds = boto3.client('rds')
    lambdaFunc = boto3.client('lambda')
    print ('Trying to get Environment variable')
    try:
        funcResponse = lambdaFunc.get_function_configuration(
            FunctionName='RDS_Instance_Modification_Function'
       )
        DBinstance = funcResponse['Environment']['Variables']['DBInstanceName']
        DBinstanceClass = funcResponse['Environment']['Variables']['DBinstanceClass']
        
        print (f'Starting RDS service for DBInstance : {DBinstance}')
        print (f'RDS instance class : {DBinstanceClass}')
     
        
        response = rds.modify_db_instance(DBInstanceIdentifier=DBinstance, DBInstanceClass=DBinstanceClass, ApplyImmediately=True)
        
        print (f'Success :: {response} ') 
        return json.dumps(dict(abc=123))
    except ClientError as e:
return json.dumps(dict(error=str(e)))
    
    return json.dumps(dict(abc=12345))
```
<br>

**Creating Environment Variables:**

![alt text](https://github.com/yelinaung-openlab/AWS-RDS-Aurora-Lab/blob/main/RDS-AutoScaleUpDown-with-Lambda/11-LambdaFunctionCode.png?raw=true)
<br>

## Testing the Lambda Function

> Screenshot: 12-LambdaTesting.png
![alt text](https://github.com/yelinaung-openlab/AWS-RDS-Aurora-Lab/blob/main/RDS-AutoScaleUpDown-with-Lambda/12-LambdaTesting.png?raw=true)
<br>

> Screenshot: 13-LambdaTesting.png
![alt text](https://github.com/yelinaung-openlab/AWS-RDS-Aurora-Lab/blob/main/RDS-AutoScaleUpDown-with-Lambda/13-LambdaTesting.png?raw=true)
<br>

## Add the trigger for Lambda

In this session, we will add Trigger in Lambda function to run with schedule. We will use AWS EventBridge (CloudWatch Event).

**Reference Link:** https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-cron-expressions.html

> Screenshot: 14-Trigger.png
![alt text](https://github.com/yelinaung-openlab/AWS-RDS-Aurora-Lab/blob/main/RDS-AutoScaleUpDown-with-Lambda/14-Trigger.png?raw=true)

> Screenshot: 15-Trigger.png
![alt text](https://github.com/yelinaung-openlab/AWS-RDS-Aurora-Lab/blob/main/RDS-AutoScaleUpDown-with-Lambda/15-Trigger.png?raw=true)

> Screenshot: 16-Trigger.png
![alt text](https://github.com/yelinaung-openlab/AWS-RDS-Aurora-Lab/blob/main/RDS-AutoScaleUpDown-with-Lambda/16-Trigger.png?raw=true)

**NOTE:** Cron job time is running with UTC timezone. We can't change the timezone. That's why, kindly calculate different time between UTC timezone and your current timezone.



