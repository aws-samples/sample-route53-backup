# Route 53 Automated Backup with Lambda and EventBridge

## Overview

This solution helps protect against Route 53 data deletion by automating backups of Route 53 on a defined schedule. Backups allow for a point in time snapshot of the Route 53 zone along with the DNS record data to restore records that may be removed accidentally. Route 53 backups are performed by an AWS Lambda function using a Lambda with Python script. The function exports Route 53 data as CSV and JSON files and writes the output to your S3 bucket.


### Architecture and Overview
Here is the architecture of the AWS services used to build the backup solution:
![Architecture Diagram](/img/figure1.jpg)

The workflow of the automated Route 53 backup solution is as follows: 
1. EventBridge triggers the Lambda function on a specified schedule 
2. Lambda executes a script to record all DNS record data 
3. Data is stored in S3 in both JSON and CSV formats

As part of this solution, a Lambda execution role is created giving Lambda read-only access to Route53 and write access to the newly created S3 bucket used for backups, as well as access to CloudWatch logs for troubleshooting.

## Installation

The solution deployment is done through AWS CloudFormation. Follow the below steps to deploy the CloudFormation template in your AWS account.
1. Login to your AWS account and make sure you have the necessary permissions to deploy CloudFormation templates
2. Download the .yaml file in the repository
3. Navigate to the [AWS CloudFormation console](https://console.aws.amazon.com/cloudformation) in your desired region
4. Click on 'Create stack' and choose 'With new resources (standard)'
5. Choose an existing template, Upload a template file, and specify the downloaded route53-backup.yaml file and choose 'Next'
6. Name the stack, provide a name for the S3 bucket (must be a unique name) where backups will be stored and select your desired region for your bucket to reside
7. Confirm that the template may create IAM resources and Submit



## Monitoring and Maintenance
Here are a few additional steps you can take to monitor and maintain your solution to verify it is working as expected:
- Monitor the Lambda function's execution in CloudWatch Logs
- Review backup files in S3 regularly
- Consider implementing S3 lifecycle policies to manage backup retention
- Set up CloudWatch Alarms to notify you of any backup failures

Note: If you have a large amount of Route 53 zones and records, changes made to DNS while the script is running may not be recorded due to the amount of time it can take to complete

Another option would be to trigger the Route 53 backup Lambda on an event instead of a schedule. For example you could create an EventBridge rule to trigger the Lambda every time a change is made to Route53 using the following configurations:
- Rule type: Rule with an event pattern
- Events
	- Event source: AWS events or EventBridge partner events
- Event Pattern:
	- Event Source: AWS services
	- AWS Service: Route 53
	- Event Type: AWS API Call via CloudTrail
	- Specific operation(s): Enter ChangeResourceRecordSets

## Clean up
- To clean up and remove the solution, first delete all of the backup data in the backup S3 bucket
- Once the S3 bucket is empty, you can use CloudFormation to delete the original stack to remove all other resources


## License
This repo is licensed under the [MIT-0 license](/LICENSE).


## Contributors
This project is developed and maintaned by Jason Polce, Tracy Honeycutt, and Cameran Dibai