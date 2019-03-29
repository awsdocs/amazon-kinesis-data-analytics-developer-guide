# Configuring Application Output<a name="how-it-works-output"></a>

In your application code, you write the output of SQL statements to one or more in\-application streams\. You can optionally add an output configuration to your application\. to persist everything written to an in\-application stream to an external destination such as an Amazon Kinesis data stream, a Kinesis Data Firehose delivery stream, or an AWS Lambda function\. 

There is a limit on the number of external destinations you can use to persist an application output\. For more information, see [Limits](limits.md)\. 

**Note**  
We recommend that you use one external destination to persist in\-application error stream data so that you can investigate the errors\. 

In each of these output configurations, you provide the following:
+ **In\-application stream name** – The stream that you want to persist to an external destination\. 

  Kinesis Data Analytics looks for the in\-application stream that you specified in the output configuration\. \(The stream name is case sensitive and must match exactly\.\) Make sure that your application code creates this in\-application stream\. 
+ **External destination** – You can persist data to a Kinesis data stream, a Kinesis Data Firehose delivery stream, or a Lambda function\. You provide the Amazon Resource Name \(ARN\) of the stream or function\. You also provide an IAM role that Kinesis Data Analytics can assume to write to the stream or function on your behalf\. You describe the record format \(JSON, CSV\) to Kinesis Data Analytics to use when writing to the external destination\.

If Kinesis Data Analytics can't write to the streaming or Lambda destination, the service continues to try indefinitely\. This creates back pressure, causing your application to fall behind\. If this issue is not resolved, your application eventually stops processing new data\. You can monitor [Kinesis Data Analytics Metrics](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/aka-metricscollected.html) and set alarms for failures\. For more information about metrics and alarms, see [Using Amazon CloudWatch Metrics](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/working_with_metrics.html) and [Creating Amazon CloudWatch Alarms](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/AlarmThatSendsEmail.html)\.

You can configure the application output using the AWS Management Console\. The console makes the API call to save the configuration\. 

## Creating an Output Using the AWS CLI<a name="how-it-works-output-cli"></a>

This section describes how to create the `Outputs` section of the request body for a `CreateApplication` or `AddApplicationOutput` operation\.

### Creating a Kinesis Stream Output<a name="how-it-works-output-cli-streams"></a>

The following JSON fragment shows the `Outputs` section in the `CreateApplication` request body for creating an Amazon Kinesis data stream destination\.

```
"Outputs": [
   {
       "DestinationSchema": {
           "RecordFormatType": "string"
       },
       "KinesisStreamsOutput": {
           "ResourceARN": "string",
           "RoleARN": "string"
       },
       "Name": "string"
   }
 
]
```

### Creating a Kinesis Data Firehose Delivery Stream Output<a name="how-it-works-output-cli-firehose"></a>

The following JSON fragment shows the `Outputs` section in the `CreateApplication` request body for creating an Amazon Kinesis Data Firehose delivery stream destination\.

```
"Outputs": [
   {
       "DestinationSchema": {
           "RecordFormatType": "string"
       },
       "KinesisFirehoseOutput": {
           "ResourceARN": "string",
           "RoleARN": "string"
       },
       "Name": "string"
   }
]
```

### Creating a Lambda Function Output<a name="how-it-works-output-cli-lambda"></a>

The following JSON fragment shows the `Outputs` section in the `CreateApplication` request body for creating an AWS Lambda function destination\.

```
"Outputs": [
   {
       "DestinationSchema": {
           "RecordFormatType": "string"
       },
       "LambdaOutput": {
           "ResourceARN": "string",
           "RoleARN": "string"
       },
       "Name": "string"
   }
]
```