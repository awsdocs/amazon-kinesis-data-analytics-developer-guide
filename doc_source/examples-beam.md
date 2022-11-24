# Example: Using Apache Beam<a name="examples-beam"></a>

In this exercise, you create a Kinesis Data Analytics application that transforms data using [Apache Beam](https://beam.apache.org/)\. Apache Beam is a programming model for processing streaming data\. For information about using Apache Beam with Kinesis Data Analytics, see [Using Apache Beam](how-creating-apps-beam.md)\.

**Note**  
To set up required prerequisites for this exercise, first complete the [Getting Started \(DataStream API\)](getting-started.md) exercise\.

**Topics**
+ [Create Dependent Resources](#examples-beam-resources)
+ [Write Sample Records to the Input Stream](#examples-beam-write)
+ [Download and Examine the Application Code](#examples-beam-download)
+ [Compile the Application Code](#examples-beam-compile)
+ [Upload the Apache Flink Streaming Java Code](#examples-beam-upload)
+ [Create and Run the Kinesis Data Analytics Application](#examples-beam-create-run)
+ [Clean Up AWS Resources](#examples-beam-cleanup)
+ [Next Steps](#examples-beam-nextsteps)

## Create Dependent Resources<a name="examples-beam-resources"></a>

Before you create a Kinesis Data Analytics application for this exercise, you create the following dependent resources: 
+ Two Kinesis data streams \(`ExampleInputStream` and `ExampleOutputStream`\)
+ An Amazon S3 bucket to store the application's code \(`ka-app-code-<username>`\) 

You can create the Kinesis streams and Amazon S3 bucket using the console\. For instructions for creating these resources, see the following topics:
+ [Creating and Updating Data Streams](https://docs.aws.amazon.com/kinesis/latest/dev/amazon-kinesis-streams.html) in the *Amazon Kinesis Data Streams Developer Guide*\. Name your data streams **ExampleInputStream** and **ExampleOutputStream**\.
+ [How Do I Create an S3 Bucket?](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/create-bucket.html) in the *Amazon Simple Storage Service User Guide*\. Give the Amazon S3 bucket a globally unique name by appending your login name, such as **ka\-app\-code\-*<username>***\.

## Write Sample Records to the Input Stream<a name="examples-beam-write"></a>

In this section, you use a Python script to write random strings to the stream for the application to process\.

**Note**  
This section requires the [AWS SDK for Python \(Boto\)](https://aws.amazon.com/developers/getting-started/python/)\.

1. Create a file named `ping.py` with the following contents:

   ```
   import json
   import boto3
   import random
   
   kinesis = boto3.client('kinesis')
   
   while True:
           data = random.choice(['ping', 'telnet', 'ftp', 'tracert', 'netstat'])
           print(data)
           kinesis.put_record(
                   StreamName="ExampleInputStream",
                   Data=data,
                   PartitionKey="partitionkey")
   ```

1. Run the `ping.py` script: 

   ```
   $ python ping.py
   ```

   Keep the script running while completing the rest of the tutorial\.

## Download and Examine the Application Code<a name="examples-beam-download"></a>

The Java application code for this example is available from GitHub\. To download the application code, do the following:

1. Install the Git client if you haven't already\. For more information, see [Installing Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)\. 

1. Clone the remote repository with the following command:

   ```
   git clone https://github.com/aws-samples/amazon-kinesis-data-analytics-java-examples
   ```

1. Navigate to the `amazon-kinesis-data-analytics-java-examples/Beam` directory\.

The application code is located in the `BasicBeamStreamingJob.java` file\. Note the following about the application code:
+ The application uses the Apache Beam [ParDo](https://beam.apache.org/releases/javadoc/2.0.0/org/apache/beam/sdk/transforms/ParDo.html) to process incoming records by invoking a custom transform function called `PingPongFn`\.

  The code to invoke the `PingPongFn` function is as follows:

  ```
  .apply("Pong transform",
      ParDo.of(new PingPongFn())
  ```
+ Kinesis Data Analytics applications that use Apache Beam require the following components\. If you don't include these components and versions in your `pom.xml`, your application loads the incorrect versions from the environment dependencies, and since the versions do not match, your application crashes at runtime\.

  ```
  <jackson.version>2.10.2</jackson.version>
  ...
  <dependency>
      <groupId>com.fasterxml.jackson.module</groupId>
      <artifactId>jackson-module-jaxb-annotations</artifactId>
      <version>2.10.2</version>
  </dependency>
  ```
+ The `PingPongFn` transform function passes the input data into the output stream, unless the input data is **ping**, in which case it emits the string **pong\\n** to the output stream\. 

  The code of the transform function is as follows:

  ```
      private static class PingPongFn extends DoFn<KinesisRecord, byte[]> {
      private static final Logger LOG = LoggerFactory.getLogger(PingPongFn.class);
      
      @ProcessElement
      public void processElement(ProcessContext c) {
          String content = new String(c.element().getDataAsBytes(), StandardCharsets.UTF_8);
          if (content.trim().equalsIgnoreCase("ping")) {
              LOG.info("Ponged!");
              c.output("pong\n".getBytes(StandardCharsets.UTF_8));
          } else {
              LOG.info("No action for: " + content);
              c.output(c.element().getDataAsBytes());
          }
      }
  }
  ```

## Compile the Application Code<a name="examples-beam-compile"></a>

To compile the application, do the following:

1. Install Java and Maven if you haven't already\. For more information, see [Prerequisites](getting-started.md#setting-up-prerequisites) in the [Getting Started \(DataStream API\)](getting-started.md) tutorial\.

1. Compile the application with the following command: 

   ```
   mvn package -Dflink.version=1.13.2 -Dflink.version.minor=1.8
   ```
**Note**  
The provided source code relies on libraries from Java 11\. If you are using a development environment, 

Compiling the application creates the application JAR file \(`target/basic-beam-app-1.0.jar`\)\.

## Upload the Apache Flink Streaming Java Code<a name="examples-beam-upload"></a>

In this section, you upload your application code to the Amazon S3 bucket you created in the [Create Dependent Resources](#examples-beam-resources) section\.

1. In the Amazon S3 console, choose the **ka\-app\-code\-*<username>*** bucket, and choose **Upload**\.

1. In the **Select files** step, choose **Add files**\. Navigate to the `basic-beam-app-1.0.jar` file that you created in the previous step\. 

1. You don't need to change any of the settings for the object, so choose **Upload**\.

Your application code is now stored in an Amazon S3 bucket where your application can access it\.

## Create and Run the Kinesis Data Analytics Application<a name="examples-beam-create-run"></a>

Follow these steps to create, configure, update, and run the application using the console\.

### Create the Application<a name="examples-beam-create"></a>

1. Open the Kinesis Data Analytics console at [ https://console\.aws\.amazon\.com/kinesisanalytics](https://console.aws.amazon.com/kinesisanalytics)\.

1. On the Kinesis Data Analytics dashboard, choose **Create analytics application**\.

1. On the **Kinesis Analytics \- Create application** page, provide the application details as follows:
   + For **Application name**, enter **MyApplication**\.
   + For **Runtime**, choose **Apache Flink**\.
**Note**  
Kinesis Data Analytics uses Apache Flink version 1\.13\.2\.
   + Leave the version pulldown as **Apache Flink version 1\.13\.2 \(Recommended version\)**\.

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
                   "arn:aws:s3:::ka-app-code-<username>/basic-beam-app-1.0.jar"
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
               "Sid": "ReadInputStream",
               "Effect": "Allow",
               "Action": "kinesis:*",
               "Resource": "arn:aws:kinesis:us-west-2:012345678901:stream/ExampleInputStream"
           },
           {
               "Sid": "WriteOutputStream",
               "Effect": "Allow",
               "Action": "kinesis:*",
               "Resource": "arn:aws:kinesis:us-west-2:012345678901:stream/ExampleOutputStream"
           }
       ]
   }
   ```

### Configure the Application<a name="examples-beam-configure"></a>

1. On the **MyApplication** page, choose **Configure**\.

1. On the **Configure application** page, provide the **Code location**:
   + For **Amazon S3 bucket**, enter **ka\-app\-code\-*<username>***\.
   + For **Path to Amazon S3 object**, enter **basic\-beam\-app\-1\.0\.jar**\.

1. Under **Access to application resources**, for **Access permissions**, choose **Create / update IAM role `kinesis-analytics-MyApplication-us-west-2`**\.

1. Under **Properties**, for **Group ID**, enter **BeamApplicationProperties**\.

1. Enter the following application properties and values:    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/kinesisanalytics/latest/java/examples-beam.html)

1. Under **Monitoring**, ensure that the **Monitoring metrics level** is set to **Application**\.

1. For **CloudWatch logging**, select the **Enable** check box\.

1. Choose **Update**\.

**Note**  
When you choose to enable CloudWatch logging, Kinesis Data Analytics creates a log group and log stream for you\. The names of these resources are as follows:   
Log group: `/aws/kinesis-analytics/MyApplication`
Log stream: `kinesis-analytics-log-stream`
This log stream is used to monitor the application\. This is not the same log stream that the application uses to send results\.

### Run the Application<a name="examples-beam-run"></a>

The Flink job graph can be viewed by running the application, opening the Apache Flink dashboard, and choosing the desired Flink job\.

You can check the Kinesis Data Analytics metrics on the CloudWatch console to verify that the application is working\. 

## Clean Up AWS Resources<a name="examples-beam-cleanup"></a>

This section includes procedures for cleaning up AWS resources created in the Tumbling Window tutorial\.

**Topics**
+ [Delete Your Kinesis Data Analytics Application](#examples-beam-cleanup-app)
+ [Delete Your Kinesis Data Streams](#examples-beam-cleanup-stream)
+ [Delete Your Amazon S3 Object and Bucket](#examples-beam-cleanup-s3)
+ [Delete Your IAM Resources](#examples-beam-cleanup-iam)
+ [Delete Your CloudWatch Resources](#examples-beam-cleanup-cw)

### Delete Your Kinesis Data Analytics Application<a name="examples-beam-cleanup-app"></a>

1. Open the Kinesis Data Analytics console at [ https://console\.aws\.amazon\.com/kinesisanalytics](https://console.aws.amazon.com/kinesisanalytics)\.

1. in the Kinesis Data Analytics panel, choose **MyApplication**\.

1. In the application's page, choose **Delete** and then confirm the deletion\.

### Delete Your Kinesis Data Streams<a name="examples-beam-cleanup-stream"></a>

1. Open the Kinesis console at [https://console\.aws\.amazon\.com/kinesis](https://console.aws.amazon.com/kinesis)\.

1. In the Kinesis Data Streams panel, choose **ExampleInputStream**\.

1. In the **ExampleInputStream** page, choose **Delete Kinesis Stream** and then confirm the deletion\.

1. In the **Kinesis streams** page, choose the **ExampleOutputStream**, choose **Actions**, choose **Delete**, and then confirm the deletion\.

### Delete Your Amazon S3 Object and Bucket<a name="examples-beam-cleanup-s3"></a>

1. Open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. Choose the **ka\-app\-code\-*<username>* bucket\.**

1. Choose **Delete** and then enter the bucket name to confirm deletion\.

### Delete Your IAM Resources<a name="examples-beam-cleanup-iam"></a>

1. Open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. In the navigation bar, choose **Policies**\.

1. In the filter control, enter **kinesis**\.

1. Choose the **kinesis\-analytics\-service\-MyApplication\-*<your\-region>*** policy\.

1. Choose **Policy Actions** and then choose **Delete**\.

1. In the navigation bar, choose **Roles**\.

1. Choose the **kinesis\-analytics\-MyApplication\-*<your\-region>*** role\.

1. Choose **Delete role** and then confirm the deletion\.

### Delete Your CloudWatch Resources<a name="examples-beam-cleanup-cw"></a>

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. In the navigation bar, choose **Logs**\.

1. Choose the **/aws/kinesis\-analytics/MyApplication** log group\.

1. Choose **Delete Log Group** and then confirm the deletion\.

## Next Steps<a name="examples-beam-nextsteps"></a>

Now that you've created and run a basic Kinesis Data Analytics application that transforms data using Apache Beam, see the following application for an example of a more advanced Kinesis Data Analytics solution\.
+ ** [ Beam on Kinesis Data Analytics Streaming Workshop](https://streaming-analytics.workshop.aws/beam-on-kda/)**: In this workshop, we explore an end to end example that combines batch and streaming aspects in one uniform Apache Beam pipeline\. 