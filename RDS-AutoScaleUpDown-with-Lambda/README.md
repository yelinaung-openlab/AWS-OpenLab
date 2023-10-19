# Scaling Up and Down for RDS instance using Lambda Function

This guideline is providing how to scale up and down (resizing) RDS instance size automatically using Lambda function.

## Steps

- [Setup the required IAM policy](#SetUp_IAMpolicy)
- [Setup the required IAM roles and attach policy](#IAMroles)
- [Create an AWS Lambda Function](#Create_an_Lambda_Function)
- [Add Inline Policy](#Inline_Policy)
- [Function Code for Lambda](#Function_code_for_lambda)
- [TestingTime] (#Testing_Lambda_function)

## Setup IAM Policy

Provide step-by-step instructions on how to install and set up your project. You can include code snippets, dependencies, or other relevant information.

```bash
# Example installation commands
$ git clone https://github.com/yourusername/your-project.git
$ cd your-project
$ npm install

## Setup IAM Roles