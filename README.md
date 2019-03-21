# aws_lambda_s3_link

Project contains AWS S3 bucket and AWS Lambda, subscribed to s3 3:ObjectCreated event. After fiering, lambda generates pre-signed link to this object, and send it in EMail, using SES. 

![aws_lambda_s3_link](https://raw.githubusercontent.com/alexandre-senko/aws_lambda_s3_link/master/s3_link_generator.jpg)

Your file sholud be uploaded into the 'FileExchangeBucket' bucket, which is private with the SSE encryption. Uploading file will led to invocation AWS Lambda 'LinkGenerator'. Lambda's Python code will generate pre-signed link to download file from S3 and this link will be added as a parameter into AWS SES SDK, and will be used to send EMail into 'EmailRecipient', which body was created from the 'LinkTemplate' html template 

File into 'FileExchangeBucket' will be automatically removed after the 'ExchangableObjectsTTL' days. Link to file is a temporary as well - it will expire after the 'LinkExpirationPeriod' seconds

Access logs for the 'FileExchangeBucket' will be stored into the 'FileExchangeBucketLogs' bucket. 

If 'LinkGenerator', for some reason, will throw error, invocation event will be delivered into the SQS 'DeadLetterQueue'. When queue length become more than 'DeadLetterQueueAlarmTreshold', alarm will be delivered into SNS topic 'CommonAlarmTopic', and it will be send to 'AdminAlarmEMail'   

Events from 'LinkGenerator' traced with using the X-Ray. 

## Getting Started


### Prerequisites

First of all, you should [verify](https://docs.aws.amazon.com/en_us/ses/latest/DeveloperGuide/verify-email-addresses.html) both EMail addresses - 'EmailRecipient' and 'EmailSender'. 


### Deployment

1) Apply the 'ses_email_sender.yaml' CloudFormation template, and click on confirmation link into SNS email, recieved in the 'AdminAlarmEMail' mailbox

2) Apply the 'lambda_link_generator.yaml' CloudFormation template.


## Built With

Used following snippets of a AWS CloudFromation templates 
* [Python sample for sending EMail through SES template]( https://docs.aws.amazon.com/en_us/code-samples/latest/catalog/python-ses-ses_sendtemplatedemail.py.html)
* [Python sample for creating pre-signed temporary link to S3 file](https://aws.amazon.com/ru/premiumsupport/knowledge-center/presigned-url-s3-bucket-expiration/)

## Authors

* **ALexandre Senko** - [alexandre-senko](https://github.com/alexandre-senko/)


## License

This project is licensed under the MIT License - see the [LICENSE.md](LICENSE.md) file for details


