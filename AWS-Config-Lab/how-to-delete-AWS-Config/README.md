# How to delete AWS Config aka how to trun off AWS config

This guideline is providing how to turn off or delete AWS config

## Steps

- Turn Off Recording Off 
- Delete the Rule 
- Use the AWS CLI and delete default recording
- Delete the service linked role created for AWS config

## Turn Off Recording Off
To delete the AWS config, firstly, have to turn off the AWS config recording under the **Setting** menu.

1. Go to AWS Config Service
2. In the left menu, click the **Setting** button
3. Turn off the **Recorder**

> Screenshot: 01-awsconfigturnoff.png

![alt text](https://github.com/yelinaung-openlab/AWS-OpenLab/blob/main/AWS-Config-Lab/how-to-delete-AWS-Config/01-awsconfigturnoff.png?raw=true)

> Screenshot: 02-awsconfigturnoff.png

![alt text](https://github.com/yelinaung-openlab/AWS-OpenLab/blob/main/AWS-Config-Lab/how-to-delete-AWS-Config/02-awsconfigturnoff.png?raw=true)

> Screenshot: 03-awsconfigturnoff.png

![alt text](https://github.com/yelinaung-openlab/AWS-OpenLab/blob/main/AWS-Config-Lab/how-to-delete-AWS-Config/03-awsconfigturnoff.png?raw=true)

**ADDITIONAL COMMAND**

If you want to **stop** the recorder from CLI, use the following command:
```bash
$ aws configservice stop-configuration-recorder --configuration-recorder-name default --region <your-config-region> --profile <your-aws-cli-profile-name>
```

If you want to **start** the recorder from CLI, use the following command:
```bash
$ aws configservice start-configuration-recorder --configuration-recorder-name default --region <your-config-region> --profile <your-aws-cli-profile-name>
```

## Delete the Rule
Second step is delete the config rule.
In the left menu, click the rules and delete all rules.

> Screenshot: 04-awsconfigturnoff.png

![alt text](https://github.com/yelinaung-openlab/AWS-OpenLab/blob/main/AWS-Config-Lab/how-to-delete-AWS-Config/04-awsconfigturnoff.png?raw=true)


## Use the AWS CLI and delete default recording

To delete the AWS default config, have to use AWS CLI.

Use the following commands to delete the default config.

```bash
$ aws configservice delete-configuration-recorder --configuration-recorder-name default --region <your-config-region-name> 
```
**NOTE** If you are using with multi aws cli profile in your machine, you have to point your aws account profile name in the command line:
```bash
$aws configservice delete-configuration-recorder --configuration-recorder-name default --region <your-config-region-name> --profile <your-aws-account-cli-profile-name>
````
> Screenshot: 05-awsconfigturnoff.png

![alt text](https://github.com/yelinaung-openlab/AWS-OpenLab/blob/main/AWS-Config-Lab/how-to-delete-AWS-Config/05-awsconfigturnoff.png?raw=true)

> Screenshot: 06-awsconfigturnoff.png

![alt text](https://github.com/yelinaung-openlab/AWS-OpenLab/blob/main/AWS-Config-Lab/how-to-delete-AWS-Config/06-awsconfigturnoff.png?raw=true)


## Delete the service linked role created for AWS config

Do not forget to delete the service role that used for AWS config.

> Screenshot: 07-awsconfigturnoff.png

![alt text](https://github.com/yelinaung-openlab/AWS-OpenLab/blob/main/AWS-Config-Lab/how-to-delete-AWS-Config/07-awsconfigturnoff.png?raw=true)
