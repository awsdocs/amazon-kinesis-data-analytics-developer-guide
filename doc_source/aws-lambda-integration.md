# Example: Integrating Amazon Kinesis Data Analytics with AWS Lambda<a name="aws-lambda-integration"></a>

Integrating Amazon Kinesis Data Analytics applications with AWS Lambda enable additional scenarios\. If you persist your application output an Kinesis stream, you can have AWS Lambda poll the stream and invoke a Lambda function\. Your Lambda function can then process records that arrive on the stream, for example write those records to a destination of your choice\.

The example Amazon Kinesis Data Analytics application in the following sections persist output to an Kinesis stream: 

+ [Example: Simple Alerts](app-simple-alerts.md)

+  [Example: Detecting Data Anomalies on a Stream \(the RANDOM\_CUT\_FOREST Function\)](app-anomaly-detection.md)

You can further enhance these examples using AWS Lambda to publish alerts\. For illustration, this section shows how to create a Lambda function and configure AWS Lambda so you get email notifications when records arrive at the Amazon Kinesis Data Analytics stream\. 

You configure AWS Lambda as follows:

+ Configure Lambda to poll the Kinesis stream and invoke your Lambda function when new records are detected\. The Lambda function receives these new records as the *event* parameter\.

   

+ Write a Lambda function to process the events\. In this example, the Lambda function publishes a message to an Amazon Simple Notification Service \(Amazon SNS\) topic\. 

  For testing, you subscribe to the topic using email protocol\. Amazon SNS then notifies you whenever the Lambda function publishes a message \(an alert\) to the Amazon SNS topic\.

   

+ Add event source mapping in AWS Lambda to associate the Lambda function with your Kinesis stream\. 

**Note**  
The instructions in this exercise use the US East \(N\. Virginia\) Region, \(`us-east-1`\)\. 

**About AWS Lambda**  
If you are new to AWS Lambda, we recommend that you read the overview topic [What IS AWS Lambda?](http://docs.aws.amazon.com/lambda/latest/dg/) in the *AWS Lambda Developer Guide*\. The [Using AWS Lambda with Kinesis](http://docs.aws.amazon.com/lambda/latest/dg/with-kinesis.html) chapter also provides an AWS Lambda and Amazon Kinesis Data Analytics integration example that you might find useful\. However, that example uses the AWS CLI\. In this exercise you use the AWS Lambda console to quickly create a Lambda function and map it to the destination stream of your application\.


+ [Step 1: Create an Amazon Kinesis Data Analytics Application](#aws-lambda-integration-step1)
+ [Step 2: Create an Amazon SNS Topic](#aws-lambda-integration-step2)
+ [Step 3: Create a Lambda Function](#aws-lambda-integration-step3)
+ [Step 4: Verify Results](#aws-lambda-integration-step4)

## Step 1: Create an Amazon Kinesis Data Analytics Application<a name="aws-lambda-integration-step1"></a>

In this section you set up an Amazon Kinesis Data Analytics application as follows:

1. First set up the example application that assigns anomaly score to heart rate data on a stream\. For instructions, see [Example: Detecting Data Anomalies on a Stream \(the RANDOM\_CUT\_FOREST Function\)](app-anomaly-detection.md)\.

1. You now update part of the application code that writes rows to the DESTINATION\_SQL\_STREAM stream\. Now you want application to write only rows with higher anomaly score to the DESTINATION\_SQL\_STREAM\. 

   ```
   CREATE OR REPLACE PUMP "OUTPUT_PUMP" AS 
      INSERT INTO "DESTINATION_SQL_STREAM"
         SELECT STREAM * FROM "TEMP_STREAM"
         WHERE "ANOMALY_SCORE" > 3.0;
   ```

Here we choose, 3\.0 anomaly score, you can tweak this value as needed\. The idea is to have the application write high heart rate records to the output\.

## Step 2: Create an Amazon SNS Topic<a name="aws-lambda-integration-step2"></a>

Create an Amazon SNS topic and subscribe to it using the email as the protocol\. Your Lambda function will post messages to the topic and you will get email notifications\. For instructions, see [Getting Started with Amazon Simple Notification Service](http://docs.aws.amazon.com/sns/latest/dg/GettingStarted.html) in the *Amazon Simple Notification Service Developer Guide*\.

## Step 3: Create a Lambda Function<a name="aws-lambda-integration-step3"></a>

In this step, you do two things—create a Lambda function and then map your application destination stream as the event source for your Lambda function\. 

If you are new to AWS Lambda, we recommend that you first review [AWS Lambda: How It Works](http://docs.aws.amazon.com/lambda/latest/dg/lambda-introduction.html) in the *AWS Lambda Developer Guide*\.

In the AWS Lambda console at [https://console\.aws\.amazon\.com/lambda/](https://console.aws.amazon.com/lambda/), choose **Create Function** and then follow these steps:

1. On the **Step 1: Select blueprint** page, select the **kinesis\-process\-record\-python** blueprint\. This blueprint closely resembles the scenario in this exercise\.

1. On the **Step2: Configure event sources** page, specify the following values:

   + **Event source type** – Kinesis

   + **Kinesis stream** – Select the Kinesis stream from the that is the configured destination for your Amazon Kinesis Data Analytics application\.

   + **Batch size** – 1

1. On the **Step 3: Configure function** page, specify following values:

   **Name** – ProcessAnomalies

   **Runtime** – Python 2\.7\.

   Replace the sample code by the following:

   ```
   import base64
   import json
   import boto3
   
   snsClient = boto3.client('sns')
   print('Loading function')
   
   def lambda_handler(event, context):
       for record in event['Records']:
           # Kinesis data is base64 encoded so decode here
           # payload = json.loads(base64.b64decode(record['kinesis']['data']))
           payload = base64.b64decode(record['kinesis']['data'])
           print payload
           response = snsClient.publish(
           TopicArn='SNS-topic-ARN',
           Message='Anomaly detected ...  ' + payload,
           Subject='Anomaly detected',
           MessageStructure='string',
           MessageAttributes={
           'String': {
               'DataType': 'String',
               'StringValue': 'New records have been processed.'
               }
           }
       )
       return 'Successfully processed {} records.'.format(len(event['Records']))
   ```
**Note**  
You need to update the code by providing the `TopicArn`\.

   **Role** – Choose **Kinesis execution role**\. On the detail page that appears, choose **View Policy Document**, and then choose **edit**\. Add permission for the **sns:Publish** action\. This allows the Lambda function to publish the anomaly event to the specific Amazon SNS topic\.

   **Timeout** –60 seconds

   Leave the default values for the other fields\.

1. Choose **Create function** to create the Lambda function\.

1. On the **Event sources** tab for the Lambda function, verify that the specific event source is **enabled**\.

   You now have a Lambda function created and it is mapped to the destination stream of your application\. AWS Lambda now begins polling the destination stream, and invokes your Lambda function when records appear on the stream\. 

## Step 4: Verify Results<a name="aws-lambda-integration-step4"></a>

If all is well, you have the following occurring in your application flow:

+ Sample script is writing data to your application's streaming source\.

+ Your application is processing records on the streaming source \(assigning anomaly score to each record based on the hear rate\), and writing records with anomaly scores to in\-application output stream\. 

+ Amazon Kinesis Data Analytics is writing records from the in\-application output stream to the output destination \(an Kinesis stream\) configured for your application\.

+ AWS Lambda is polling your destination stream and invoking your Lambda function\. Your Lambda function will process each record, and publish a message to your Amazon SNS topic\.

+ Amazon SNS is sending email notifications to you\.

If you don't get Amazon SNS email notifications, you can check the logs in the CloudWatch log for your application\. The logs provide information that can help you debug the problem\. For example, your Lambda function might be posting messages to the Amazon SNS topic, but you have not subscribed to the topic \(or you subscribed to the topic, but did not confirm the subscription\)\. The log provides useful information that will help you fix the problem\.