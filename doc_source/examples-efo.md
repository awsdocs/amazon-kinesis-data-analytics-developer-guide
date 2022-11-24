# Example: Use an EFO Consumer with a Kinesis Data Stream<a name="examples-efo"></a>

In this exercise, you create a Kinesis Data Analytics application that reads from a Kinesis Data Stream using an [ Enhanced Fan\-Out \(EFO\)](https://docs.aws.amazon.com/streams/latest/dev/enhanced-consumers.html) consumer\. If a Kinesis consumer uses EFO, the Kinesis Data Streams service gives it its own dedicated bandwidth, rather than having the consumer share the fixed bandwidth of the stream with the other consumers reading from the stream\.

For more information about using EFO with the Kinesis consumer, see [ FLIP\-128: Enhanced Fan Out for Kinesis Consumers](https://cwiki.apache.org/confluence/display/FLINK/FLIP-128%3A+Enhanced+Fan+Out+for+AWS+Kinesis+Consumers)\.

The application you create in this example uses AWS Kinesis Connector \(flink\-connector\-kinesis\) 1\.15\.2\.

**Note**  
To set up required prerequisites for this exercise, first complete the [Getting Started \(DataStream API\)](getting-started.md) exercise\.

**Topics**
+ [Create Dependent Resources](#examples-efo-resources)
+ [Write Sample Records to the Input Stream](#examples-efo-write)
+ [Download and Examine the Application Code](#examples-efo-download)
+ [Compile the Application Code](#examples-efo-compile)
+ [Upload the Apache Flink Streaming Java Code](#examples-efo-upload)
+ [Create and Run the Kinesis Data Analytics Application](#examples-efo-create-run)
+ [Clean Up AWS Resources](#examples-efo-cleanup)

## Create Dependent Resources<a name="examples-efo-resources"></a>

Before you create a Kinesis Data Analytics application for this exercise, you create the following dependent resources: 
+ Two Kinesis data streams \(`ExampleInputStream` and `ExampleOutputStream`\)
+ An Amazon S3 bucket to store the application's code \(`ka-app-code-<username>`\) 

You can create the Kinesis streams and Amazon S3 bucket using the console\. For instructions for creating these resources, see the following topics:
+ [Creating and Updating Data Streams](https://docs.aws.amazon.com/kinesis/latest/dev/amazon-kinesis-streams.html) in the *Amazon Kinesis Data Streams Developer Guide*\. Name your data stream **ExampleInputStream** and **ExampleOutputStream**\.
+ [How Do I Create an S3 Bucket?](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/create-bucket.html) in the *Amazon Simple Storage Service User Guide*\. Give the Amazon S3 bucket a globally unique name by appending your login name, such as **ka\-app\-code\-*<username>***\.

## Write Sample Records to the Input Stream<a name="examples-efo-write"></a>

In this section, you use a Python script to write sample records to the stream for the application to process\.

**Note**  
This section requires the [AWS SDK for Python \(Boto\)](https://aws.amazon.com/developers/getting-started/python/)\.

1. Create a file named `stock.py` with the following contents:

   ```
    import datetime
       import json
       import random
       import boto3
   
       STREAM_NAME = "ExampleInputStream"
   
   
       def get_data():
           return {
               'event_time': datetime.datetime.now().isoformat(),
               'ticker': random.choice(['AAPL', 'AMZN', 'MSFT', 'INTC', 'TBV']),
               'price': round(random.random() * 100, 2)}
   
   
       def generate(stream_name, kinesis_client):
           while True:
               data = get_data()
               print(data)
               kinesis_client.put_record(
                   StreamName=stream_name,
                   Data=json.dumps(data),
                   PartitionKey="partitionkey")
   
   
       if __name__ == '__main__':
           generate(STREAM_NAME, boto3.client('kinesis', region_name='us-west-2'))
   ```

1. Run the `stock.py` script: 

   ```
   $ python stock.py
   ```

   Keep the script running while completing the rest of the tutorial\.

## Download and Examine the Application Code<a name="examples-efo-download"></a>

The Java application code for this example is available from GitHub\. To download the application code, do the following:

1. Install the Git client if you haven't already\. For more information, see [Installing Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)\. 

1. Clone the remote repository with the following command:

   ```
   git clone https://github.com/aws-samples/amazon-kinesis-data-analytics-java-examples
   ```

1. Navigate to the `amazon-kinesis-data-analytics-java-examples/EfoConsumer` directory\.

The application code is located in the `EfoApplication.java` file\. Note the following about the application code:
+ You enable the EFO consumer by setting the following parameters on the Kinesis consumer:
  + **RECORD\_PUBLISHER\_TYPE: ** Set this parameter to **EFO** for your application to use an EFO consumer to access the Kinesis Data Stream data\. 
  + **EFO\_CONSUMER\_NAME: ** Set this parameter to a string value that is unique among the consumers of this stream\. Re\-using a consumer name in the same Kinesis Data Stream will cause the previous consumer using that name to be terminated\. 
+ The following code example demonstrates how to assign values to the consumer configuration properties to use an EFO consumer to read from the source stream:

  ```
  consumerConfig.putIfAbsent(RECORD_PUBLISHER_TYPE, "EFO");
  consumerConfig.putIfAbsent(EFO_CONSUMER_NAME, "basic-efo-flink-app");
  ```

## Compile the Application Code<a name="examples-efo-compile"></a>

To compile the application, do the following:

1. Install Java and Maven if you haven't already\. For more information, see [Prerequisites](getting-started.md#setting-up-prerequisites) in the [Getting Started \(DataStream API\)](getting-started.md) tutorial\.

1. Compile the application with the following command: 

   ```
   mvn package -Dflink.version=1.15.2
   ```
**Note**  
The provided source code relies on libraries from Java 11\. 

Compiling the application creates the application JAR file \(`target/aws-kinesis-analytics-java-apps-1.0.jar`\)\.

## Upload the Apache Flink Streaming Java Code<a name="examples-efo-upload"></a>

In this section, you upload your application code to the Amazon S3 bucket you created in the [Create Dependent Resources](#examples-efo-resources) section\.

1. In the Amazon S3 console, choose the **ka\-app\-code\-*<username>*** bucket, and choose **Upload**\.

1. In the **Select files** step, choose **Add files**\. Navigate to the `aws-kinesis-analytics-java-apps-1.0.jar` file that you created in the previous step\. 

1. You don't need to change any of the settings for the object, so choose **Upload**\.

Your application code is now stored in an Amazon S3 bucket where your application can access it\.

## Create and Run the Kinesis Data Analytics Application<a name="examples-efo-create-run"></a>

Follow these steps to create, configure, update, and run the application using the console\.

### Create the Application<a name="examples-efo-create"></a>

1. Open the Kinesis Data Analytics console at [ https://console\.aws\.amazon\.com/kinesisanalytics](https://console.aws.amazon.com/kinesisanalytics)\.

1. On the Kinesis Data Analytics dashboard, choose **Create analytics application**\.

1. On the **Kinesis Analytics \- Create application** page, provide the application details as follows:
   + For **Application name**, enter **MyApplication**\.
   + For **Runtime**, choose **Apache Flink**\.
**Note**  
Kinesis Data Analytics uses Apache Flink version 1\.15\.2\.
   + Leave the version pulldown as **Apache Flink version 1\.15\.2 \(Recommended version\)**\.

1. For **Access permissions**, choose **Create / update IAM role `kinesis-analytics-MyApplication-us-west-2`**\.

1. Choose **Create application**\.

**Note**  
When you create a Kinesis Data Analytics application using the console, you have the option of having an IAM role and policy created for your application\. Your application uses this role and policy to access its dependent resources\. These IAM resources are named using your application name and Region as follows:  
Policy: `kinesis-analytics-service-MyApplication-us-west-2`
Role: `kinesis-analytics-MyApplication-us-west-2`

### Edit the IAM Policy<a name="get-started-exercise-7-console-iam"></a>

Edit the IAM policy to add permissions to access the Kinesis data streams\.

1. Open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. Choose **Policies**\. Choose the **`kinesis-analytics-service-MyApplication-us-west-2`** policy that the console created for you in the previous section\. 

1. On the **Summary** page, choose **Edit policy**\. Choose the **JSON** tab\.

1. Add the highlighted section of the following policy example to the policy\. Replace the sample account IDs \(*012345678901*\) with your account ID\.
**Note**  
These permissions grant the application the ability to access the EFO consumer\.

   ```
   {
       "Version": "2012-10-17",
       "Statement": [
           {
               "Sid": "ReadCode",
               "Effect": "Allow",
               "Action": [
                   "s3:GetObject",
                   "logs:DescribeLogGroups",
                   "s3:GetObjectVersion"
               ],
               "Resource": [
                   "arn:aws:logs:us-west-2:012345678901:log-group:*",
                   "arn:aws:s3:::ka-app-code-<username>/aws-kinesis-analytics-java-apps-1.0.jar"
               ]
           },
           {
               "Sid": "DescribeLogStreams",
               "Effect": "Allow",
               "Action": "logs:DescribeLogStreams",
               "Resource": "arn:aws:logs:us-west-2:012345678901:log-group:/aws/kinesis-analytics/MyApplication:log-stream:*"
           },
           {
               "Sid": "PutLogEvents",
               "Effect": "Allow",
               "Action": "logs:PutLogEvents",
               "Resource": "arn:aws:logs:us-west-2:012345678901:log-group:/aws/kinesis-analytics/MyApplication:log-stream:kinesis-analytics-log-stream"
           },
           {
               "Sid": "ListCloudwatchLogGroups",
               "Effect": "Allow",
               "Action": [
                   "logs:DescribeLogGroups"
               ],
               "Resource": [
                   "arn:aws:logs:us-west-2:012345678901:log-group:*"
               ]
           },
           {
               "Sid": "AllStreams",
               "Effect": "Allow",
               "Action": [
                   "kinesis:ListShards",
                   "kinesis:ListStreamConsumers",
                   "kinesis:DescribeStreamSummary"
               ],
               "Resource": "arn:aws:kinesis:us-west-2:012345678901:stream/*"
           },
           {
               "Sid": "Stream",
               "Effect": "Allow",
               "Action": [
                   "kinesis:DescribeStream",
                   "kinesis:RegisterStreamConsumer",
                   "kinesis:DeregisterStreamConsumer"
               ],
               "Resource": "arn:aws:kinesis:us-west-2:012345678901:stream/ExampleInputStream"
           },
           {
               "Sid": "WriteOutputStream",
               "Effect": "Allow",
               "Action": "kinesis:*",
               "Resource": "arn:aws:kinesis:us-west-2:012345678901:stream/ExampleOutputStream"
           },
           {
               "Sid": "Consumer",
               "Effect": "Allow",
               "Action": [
                   "kinesis:DescribeStreamConsumer",
                   "kinesis:SubscribeToShard"
               ],
               "Resource": [
                   "arn:aws:kinesis:us-west-2:012345678901:stream/ExampleInputStream/consumer/my-efo-flink-app",
                   "arn:aws:kinesis:us-west-2:012345678901:stream/ExampleInputStream/consumer/my-efo-flink-app:*"
               ]
           }
       ]
   }
   ```

### Configure the Application<a name="examples-efo-configure"></a>

1. On the **MyApplication** page, choose **Configure**\.

1. On the **Configure application** page, provide the **Code location**:
   + For **Amazon S3 bucket**, enter **ka\-app\-code\-*<username>***\.
   + For **Path to Amazon S3 object**, enter **aws\-kinesis\-analytics\-java\-apps\-1\.0\.jar**\.

1. Under **Access to application resources**, for **Access permissions**, choose **Create / update IAM role `kinesis-analytics-MyApplication-us-west-2`**\.

1. Under **Properties**, choose **Create Group**\. 

1. Enter the following application properties and values:    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/kinesisanalytics/latest/java/examples-efo.html)

1. Under **Properties**, choose **Create Group**\. 

1. Enter the following application properties and values:    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/kinesisanalytics/latest/java/examples-efo.html)

1. Under **Monitoring**, ensure that the **Monitoring metrics level** is set to **Application**\.

1. For **CloudWatch logging**, select the **Enable** check box\.

1. Choose **Update**\.

**Note**  
When you choose to enable CloudWatch logging, Kinesis Data Analytics creates a log group and log stream for you\. The names of these resources are as follows:   
Log group: `/aws/kinesis-analytics/MyApplication`
Log stream: `kinesis-analytics-log-stream`
This log stream is used to monitor the application\. This is not the same log stream that the application uses to send results\.

### Run the Application<a name="examples-efo-run"></a>

The Flink job graph can be viewed by running the application, opening the Apache Flink dashboard, and choosing the desired Flink job\.

You can check the Kinesis Data Analytics metrics on the CloudWatch console to verify that the application is working\. 

You can also check the Kinesis Data Streams console, in the data stream's **Enhanced fan\-out** tab, for the name of your consumer \(*basic\-efo\-flink\-app*\)\.

## Clean Up AWS Resources<a name="examples-efo-cleanup"></a>

This section includes procedures for cleaning up AWS resources created in the efo Window tutorial\.

**Topics**
+ [Delete Your Kinesis Data Analytics Application](#examples-efo-cleanup-app)
+ [Delete Your Kinesis Data Streams](#examples-efo-cleanup-stream)
+ [Delete Your Amazon S3 Object and Bucket](#examples-efo-cleanup-s3)
+ [Delete Your IAM Resources](#examples-efo-cleanup-iam)
+ [Delete Your CloudWatch Resources](#examples-efo-cleanup-cw)

### Delete Your Kinesis Data Analytics Application<a name="examples-efo-cleanup-app"></a>

1. Open the Kinesis Data Analytics console at [ https://console\.aws\.amazon\.com/kinesisanalytics](https://console.aws.amazon.com/kinesisanalytics)\.

1. in the Kinesis Data Analytics panel, choose **MyApplication**\.

1. In the application's page, choose **Delete** and then confirm the deletion\.

### Delete Your Kinesis Data Streams<a name="examples-efo-cleanup-stream"></a>

1. Open the Kinesis console at [https://console\.aws\.amazon\.com/kinesis](https://console.aws.amazon.com/kinesis)\.

1. In the Kinesis Data Streams panel, choose **ExampleInputStream**\.

1. In the **ExampleInputStream** page, choose **Delete Kinesis Stream** and then confirm the deletion\.

1. In the **Kinesis streams** page, choose the **ExampleOutputStream**, choose **Actions**, choose **Delete**, and then confirm the deletion\.

### Delete Your Amazon S3 Object and Bucket<a name="examples-efo-cleanup-s3"></a>

1. Open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. Choose the **ka\-app\-code\-*<username>* bucket\.**

1. Choose **Delete** and then enter the bucket name to confirm deletion\.

### Delete Your IAM Resources<a name="examples-efo-cleanup-iam"></a>

1. Open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. In the navigation bar, choose **Policies**\.

1. In the filter control, enter **kinesis**\.

1. Choose the **kinesis\-analytics\-service\-MyApplication\-*<your\-region>*** policy\.

1. Choose **Policy Actions** and then choose **Delete**\.

1. In the navigation bar, choose **Roles**\.

1. Choose the **kinesis\-analytics\-MyApplication\-*<your\-region>*** role\.

1. Choose **Delete role** and then confirm the deletion\.

### Delete Your CloudWatch Resources<a name="examples-efo-cleanup-cw"></a>

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. In the navigation bar, choose **Logs**\.

1. Choose the **/aws/kinesis\-analytics/MyApplication** log group\.

1. Choose **Delete Log Group** and then confirm the deletion\.