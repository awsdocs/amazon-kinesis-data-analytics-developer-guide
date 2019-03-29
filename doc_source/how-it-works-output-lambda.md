# Using a Lambda Function as Output<a name="how-it-works-output-lambda"></a>

Using AWS Lambda as a destination allows you to more easily perform post\-processing of your SQL results before sending them to a final destination\. Common post\-processing tasks include the following:
+ Aggregating multiple rows into a single record
+ Combining current results with past results to address late\-arriving data
+ Delivering to different destinations based on the type of information
+ Record format translation \(such as translating to Protobuf\)
+ String manipulation or transformation
+ Data enrichment after analytical processing
+ Custom processing for geospatial use cases
+ Data encryption

Lambda functions can deliver analytic information to a variety of AWS services and other destinations, including the following:
+ [Amazon Simple Storage Service \(Amazon S3\)](https://docs.aws.amazon.com/AmazonS3/latest/dev/)
+ Custom APIs
+ [Amazon DynamoDB](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/)
+ [Apache Aurora](http://aurora.apache.org/)
+ [Amazon Redshift](https://docs.aws.amazon.com/redshift/latest/dg/)
+ [Amazon Simple Notification Service \(Amazon SNS\)](https://docs.aws.amazon.com/sns/latest/dg/)
+ [Amazon Simple Queue Service \(Amazon SQS\)](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/)
+ [Amazon CloudWatch](https://docs.aws.amazon.com/AmazonCloudWatch/latest/DeveloperGuide/)

For more information about creating Lambda applications, see [Getting Started with AWS Lambda](https://docs.aws.amazon.com/lambda/latest/dg/getting-started.html)\.

**Topics**
+ [Lambda as Output Permissions](#how-it-works-output-lambda-perms)
+ [Lambda as Output Metrics](#how-it-works-output-lambda-metrics)
+ [Lambda as Output Event Input Data Model and Record Response Model](#how-it-works-output-lambda-model)
+ [Lambda Output Invocation Frequency](#how-it-works-output-lambda-frequency)
+ [Adding a Lambda Function for Use as an Output](#how-it-works-output-lambda-procedure)
+ [Common Lambda as Output Failures](#how-it-works-output-lambda-troubleshooting)
+ [Creating Lambda Functions for Application Destinations](how-it-works-output-lambda-functions.md)

## Lambda as Output Permissions<a name="how-it-works-output-lambda-perms"></a>

To use Lambda as output, the application’s Lambda output IAM role requires the following permissions policy:

```
{
   "Sid": "UseLambdaFunction",
   "Effect": "Allow",
   "Action": [
       "lambda:InvokeFunction",
       "lambda:GetFunctionConfiguration"
   ],
   "Resource": "FunctionARN"
}
```

## Lambda as Output Metrics<a name="how-it-works-output-lambda-metrics"></a>

You use Amazon CloudWatch to monitor the number of bytes sent, successes and failures, and so on\. For information about CloudWatch metrics that are emitted by Kinesis Data Analytics using Lambda as output, see [Amazon Kinesis Analytics Metrics](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/aka-metricscollected.html)\.

## Lambda as Output Event Input Data Model and Record Response Model<a name="how-it-works-output-lambda-model"></a>

To send Kinesis Data Analytics output records, your Lambda function must be compliant with the required event input data and record response models\. 

### Event Input Data Model<a name="how-it-works-output-lambda-model-request"></a>

Kinesis Data Analytics continuously sends the output records from the application to the Lambda as an output function with the following request model\. Within your function, you iterate through the list and apply your business logic to accomplish your output requirements \(such as data transformation before sending to a final destination\)\.


| Field | Description | 
| --- | --- | 
| Field | Description | 
| --- | --- | 
| Field | Description | 
| --- | --- | 
| invocationId | The Lambda invocation ID \(random GUID\)\. | 
| applicationArn | The Kinesis Data Analytics application Amazon Resource Name \(ARN\)\. | 
| records [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/kinesisanalytics/latest/dev/how-it-works-output-lambda.html)  | 
| recordId | record ID \(random GUID\) | 
| lambdaDeliveryRecordMetadata |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/kinesisanalytics/latest/dev/how-it-works-output-lambda.html)  | 
| data | Base64\-encoded output record payload | 
| retryHint | Number of delivery retries | 

**Note**  
The `retryHint` is a value that increases for every delivery failure\. This value is not durably persisted, and resets if the application is disrupted\.

### Record Response Model<a name="how-it-works-output-lambda-model-response"></a>

Each record sent to your Lambda as an output function \(with record IDs\) must be acknowledged with either `Ok` or `DeliveryFailed`, and it must contain the following parameters\. Otherwise, Kinesis Data Analytics treats them as a delivery failure\.


| Field | Description | 
| --- | --- | 
| records [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/kinesisanalytics/latest/dev/how-it-works-output-lambda.html)  | 
| recordId | The record ID is passed from Kinesis Data Analytics to Lambda during the invocation\. Any mismatch between the ID of the original record and the ID of the acknowledged record is treated as a delivery failure\. | 
| result | The status of the delivery of the record\. The following are possible values: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/kinesisanalytics/latest/dev/how-it-works-output-lambda.html)  | 

## Lambda Output Invocation Frequency<a name="how-it-works-output-lambda-frequency"></a>

A Kinesis Data Analytics application buffers the output records and invokes the AWS Lambda destination function frequently\.
+ If records are emitted to the destination in\-application stream within the data analytics application as a tumbling window, the AWS Lambda destination function is invoked per tumbling window trigger\. For example, if a tumbling window of 60 seconds is used to emit the records to the destination in\-application stream, the Lambda function is invoked once every 60 seconds\.
+ If records are emitted to the destination in\-application stream within the application as a continuous query or a sliding window, the Lambda destination function is invoked about once per second\.

**Note**  
[Per\-Lambda function invoke request payload size limits](https://docs.aws.amazon.com/lambda/latest/dg/limits.html) apply\. Exceeding those limits results in output records being split and sent across multiple Lambda function calls\.

## Adding a Lambda Function for Use as an Output<a name="how-it-works-output-lambda-procedure"></a>

The following procedure demonstrates how to add a Lambda function as an output for a Kinesis Data Analytics application\.

1. Sign in to the AWS Management Console and open the Kinesis Data Analytics console at [ https://console\.aws\.amazon\.com/kinesisanalytics](https://console.aws.amazon.com/kinesisanalytics)\.

1. Choose the application in the list, and then choose **Application details**\.

1. In the **Destination** section, choose **Connect new destination**\.

1. For the **Destination** item, choose **AWS Lambda function**\.

1. In the **Deliver records to AWS Lambda** section, either choose an existing Lambda function and version, or choose **Create new**\.

1. If you are creating a new Lambda function, do the following:

   1. Choose one of the templates provided\. For more information, [Creating Lambda Functions for Application Destinations](how-it-works-output-lambda-functions.md)\.

   1. The **Create Function** page opens in a new browser tab\. In the **Name** box, give the function a meaningful name \(for example, **myLambdaFunction**\)\.

   1. Update the template with post\-processing functionality for your application\. For information about creating a Lambda function, see [Getting Started](https://docs.aws.amazon.com/lambda/latest/dg/getting-started.html) in the *AWS Lambda Developer Guide*\.

   1. On the Kinesis Data Analytics console, in the **Lambda function** list, choose the Lambda function that you just created\. Choose **$LATEST** for the Lambda function version\.

1. In the **In\-application stream** section, choose **Choose an existing in\-application stream**\. For **In\-application stream name**, choose your application's output stream\. The results from the selected output stream are sent to the Lambda output function\.

1. Leave the rest of the form with the default values, and choose **Save and continue**\.

Your application now sends records from the in\-application stream to your Lambda function\. You can see the results of the default template in the Amazon CloudWatch console\. Monitor the `AWS/KinesisAnalytics/LambdaDelivery.OkRecords` metric to see the number of records being delivered to the Lambda function\.

## Common Lambda as Output Failures<a name="how-it-works-output-lambda-troubleshooting"></a>

The following are common reasons why delivery to a Lambda function can fail\.
+ Not all records \(with record IDs\) in a batch that are sent to the Lambda function are returned to the Kinesis Data Analytics service\. 
+ The response is missing either the record ID or the status field\. 
+ The Lambda function timeouts are not sufficient to accomplish the business logic within the Lambda function\.
+ The business logic within the Lambda function does not catch all the errors, resulting in a timeout and backpressure due to unhandled exceptions\. These are often referred as “poison pill” messages\.

For data delivery failures, Kinesis Data Analytics continues to retry Lambda invocations on the same set of records until successful\. To gain insight into failures, you can monitor the following CloudWatch metrics: 
+ Kinesis Data Analytics application Lambda as Output CloudWatch metrics: Indicates the number of successes and failures, among other statistics\. For more information, see [Amazon Kinesis Analytics Metrics](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/aka-metricscollected.html)\.
+ AWS Lambda function CloudWatch metrics and logs\.