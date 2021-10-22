# Example: Sliding Window<a name="examples-sliding"></a>

In this exercise, you create a Kinesis Data Analytics application that aggregates data using a sliding window\.

**Note**  
To set up required prerequisites for this exercise, first complete the [Getting Started \(DataStream API\)](getting-started.md) exercise\.

**Topics**
+ [Create Dependent Resources](#examples-sliding-resources)
+ [Write Sample Records to the Input Stream](#examples-sliding-write)
+ [Download and Examine the Application Code](#examples-sliding-download)
+ [Compile the Application Code](#examples-sliding-compile)
+ [Upload the Apache Flink Streaming Java Code](#examples-sliding-upload)
+ [Create and Run the Kinesis Data Analytics Application](#examples-sliding-create-run)
+ [Clean Up AWS Resources](#examples-sliding-cleanup)

## Create Dependent Resources<a name="examples-sliding-resources"></a>

Before you create a Kinesis Data Analytics application for this exercise, you create the following dependent resources: 
+ Two Kinesis data streams \(`ExampleInputStream` and `ExampleOutputStream`\)\.
+ An Amazon S3 bucket to store the application's code \(`ka-app-code-<username>`\) 

You can create the Kinesis streams and Amazon S3 bucket using the console\. For instructions for creating these resources, see the following topics:
+ [Creating and Updating Data Streams](https://docs.aws.amazon.com/kinesis/latest/dev/amazon-kinesis-streams.html) in the *Amazon Kinesis Data Streams Developer Guide*\. Name your data streams **ExampleInputStream** and **ExampleOutputStream**\.
+ [How Do I Create an S3 Bucket?](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/create-bucket.html) in the *Amazon Simple Storage Service User Guide*\. Give the Amazon S3 bucket a globally unique name by appending your login name, such as **ka\-app\-code\-*<username>***\.

## Write Sample Records to the Input Stream<a name="examples-sliding-write"></a>

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
           'EVENT_TIME': datetime.datetime.now().isoformat(),
           'TICKER': random.choice(['AAPL', 'AMZN', 'MSFT', 'INTC', 'TBV']),
           'PRICE': round(random.random() * 100, 2)}
   
   
   def generate(stream_name, kinesis_client):
       while True:
           data = get_data()
           print(data)
           kinesis_client.put_record(
               StreamName=stream_name,
               Data=json.dumps(data),
               PartitionKey="partitionkey")
   
   
   if __name__ == '__main__':
       generate(STREAM_NAME, boto3.client('kinesis'))
   ```

1. Run the `stock.py` script: 

   ```
   $ python stock.py
   ```

   Keep the script running while completing the rest of the tutorial\.

## Download and Examine the Application Code<a name="examples-sliding-download"></a>

The Java application code for this example is available from GitHub\. To download the application code, do the following:

1. Install the Git client if you haven't already\. For more information, see [Installing Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)\. 

1. Clone the remote repository with the following command:

   ```
   git clone https://github.com/aws-samples/amazon-kinesis-data-analytics-java-examples
   ```

1. Navigate to the `amazon-kinesis-data-analytics-java-examples/SlidingWindow` directory\.

The application code is located in the `SlidingWindowStreamingJobWithParallelism.java` file\. Note the following about the application code:
+ The application uses a Kinesis source to read from the source stream\. The following snippet creates the Kinesis source:

  ```
  return env.addSource(new FlinkKinesisConsumer<>(inputStreamName,
                  new SimpleStringSchema(), inputProperties));
  ```
+ The application uses the `timeWindow` operator to find the minimum value for each stock symbol over a 10\-second window that slides by 5 seconds\. The following code creates the operator and sends the aggregated data to a new Kinesis Data Streams sink:
+ Add the following import statement:

  ```
  import org.apache.flink.streaming.api.windowing.assigners.TumblingProcessingTimeWindows; //flink 1.13
  ```
+ The application uses the `timeWindow` operator to find the count of values for each stock symbol over a 5\-second tumbling window\. The following code creates the operator and sends the aggregated data to a new Kinesis Data Streams sink:

  ```
  input.flatMap(new Tokenizer()) // Tokenizer for generating words
                  .keyBy(0) // Logically partition the stream for each word
                  //.timeWindow(Time.seconds(5)) // Tumbling window definition (Flink 1.11)
  		.window(TumblingProcessingTimeWindows.of(Time.seconds(5))) //Flink 1.13
                  .sum(1) // Sum the number of words per partition
                  .map(value -> value.f0 + "," + value.f1.toString() + "\n")
                  .addSink(createSinkFromStaticConfig());
  ```

## Compile the Application Code<a name="examples-sliding-compile"></a>

To compile the application, do the following:

1. Install Java and Maven if you haven't already\. For more information, see [Prerequisites](getting-started.md#setting-up-prerequisites) in the [Getting Started \(DataStream API\)](getting-started.md) tutorial\.

1. Compile the application with the following command: 

   ```
   mvn package -Dflink.version=1.13.2
   ```
**Note**  
The provided source code relies on libraries from Java 11\. If you are using a development environment, 

Compiling the application creates the application JAR file \(`target/aws-kinesis-analytics-java-apps-1.0.jar`\)\.

## Upload the Apache Flink Streaming Java Code<a name="examples-sliding-upload"></a>

In this section, you upload your application code to the Amazon S3 bucket that you created in the [Create Dependent Resources](#examples-sliding-resources) section\.

1. In the Amazon S3 console, choose the **ka\-app\-code\-*<username>*** bucket, and then choose **Upload**\.

1. In the **Select files** step, choose **Add files**\. Navigate to the `aws-kinesis-analytics-java-apps-1.0.jar` file that you created in the previous step\. 

1. You don't need to change any of the settings for the object, so choose **Upload**\.

Your application code is now stored in an Amazon S3 bucket where your application can access it\.

## Create and Run the Kinesis Data Analytics Application<a name="examples-sliding-create-run"></a>

Follow these steps to create, configure, update, and run the application using the console\.

### Create the Application<a name="examples-sliding-create"></a>

1. Open the Kinesis Data Analytics console at [ https://console\.aws\.amazon\.com/kinesisanalytics](https://console.aws.amazon.com/kinesisanalytics)\.

1. On the Kinesis Data Analytics dashboard, choose **Create analytics application**\.

1. On the **Kinesis Analytics \- Create application** page, provide the application details as follows:
   + For **Application name**, enter **MyApplication**\.
   + For **Runtime**, choose **Apache Flink**\.
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

### Configure the Application<a name="examples-sliding-configure"></a>

1. On the **MyApplication** page, choose **Configure**\.

1. On the **Configure application** page, provide the **Code location**:
   + For **Amazon S3 bucket**, enter **ka\-app\-code\-*<username>***\.
   + For **Path to Amazon S3 object**, enter **aws\-kinesis\-analytics\-java\-apps\-1\.0\.jar**\.

1. Under **Access to application resources**, for **Access permissions**, choose **Create / update IAM role `kinesis-analytics-MyApplication-us-west-2`**\.

1. Under **Monitoring**, ensure that the **Monitoring metrics level** is set to **Application**\.

1. For **CloudWatch logging**, select the **Enable** check box\.

1. Choose **Update**\.

**Note**  
When you choose to enable Amazon CloudWatch logging, Kinesis Data Analytics creates a log group and log stream for you\. The names of these resources are as follows:   
Log group: `/aws/kinesis-analytics/MyApplication`
Log stream: `kinesis-analytics-log-stream`
This log stream is used to monitor the application\. This is not the same log stream that the application uses to send results\.

### Configure the Application Parallelism<a name="examples-sliding-parallelism"></a>

This application example uses parallel execution of tasks\. The following application code sets the parallelism of the `min` operator:

```
.setParallelism(3) // Set parallelism for the min operator
```

The application parallelism can't be greater than the provisioned parallelism, which has a default of 1\. To increase your application's parallelism, use the following AWS CLI action:

```
aws kinesisanalyticsv2 update-application
      --application-name MyApplication
      --current-application-version-id <VersionId>
      --application-configuration-update "{\"FlinkApplicationConfigurationUpdate\": { \"ParallelismConfigurationUpdate\": {\"ParallelismUpdate\": 5, \"ConfigurationTypeUpdate\": \"CUSTOM\" }}}"
```

You can retrieve the current application version ID using the [DescribeApplication](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_DescribeApplication.html) or [ListApplications](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_ListApplications.html) actions\.

### Run the Application<a name="examples-sliding-run"></a>

1. On the **MyApplication** page, choose **Run**\. Leave the **Run without snapshot** option selected, and confirm the action\.

1. When the application is running, refresh the page\. The console shows the **Application graph**\.

You can check the Kinesis Data Analytics metrics on the CloudWatch console to verify that the application is working\. 

## Clean Up AWS Resources<a name="examples-sliding-cleanup"></a>

This section includes procedures for cleaning up AWS resources created in the Sliding Window tutorial\.

**Topics**
+ [Delete Your Kinesis Data Analytics Application](#examples-sliding-cleanup-app)
+ [Delete Your Kinesis Data Streams](#examples-sliding-cleanup-stream)
+ [Delete Your Amazon S3 Object and Bucket](#examples-sliding-cleanup-s3)
+ [Delete Your IAM Resources](#examples-sliding-cleanup-iam)
+ [Delete Your CloudWatch Resources](#examples-sliding-cleanup-cw)

### Delete Your Kinesis Data Analytics Application<a name="examples-sliding-cleanup-app"></a>

1. Open the Kinesis Data Analytics console at [ https://console\.aws\.amazon\.com/kinesisanalytics](https://console.aws.amazon.com/kinesisanalytics)\.

1. In the Kinesis Data Analytics panel, choose **MyApplication**\.

1. In the application's page, choose **Delete** and then confirm the deletion\.

### Delete Your Kinesis Data Streams<a name="examples-sliding-cleanup-stream"></a>

1. Open the Kinesis console at [https://console\.aws\.amazon\.com/kinesis](https://console.aws.amazon.com/kinesis)\.

1. In the Kinesis Data Streams panel, choose **ExampleInputStream**\.

1. In the **ExampleInputStream** page, choose **Delete Kinesis Stream** and then confirm the deletion\.

1. In the **Kinesis streams** page, choose the **ExampleOutputStream**, choose **Actions**, choose **Delete**, and then confirm the deletion\.

### Delete Your Amazon S3 Object and Bucket<a name="examples-sliding-cleanup-s3"></a>

1. Open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. Choose the **ka\-app\-code\-*<username>* bucket\.**

1. Choose **Delete** and then enter the bucket name to confirm deletion\.

### Delete Your IAM Resources<a name="examples-sliding-cleanup-iam"></a>

1. Open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. In the navigation bar, choose **Policies**\.

1. In the filter control, enter **kinesis**\.

1. Choose the **kinesis\-analytics\-service\-MyApplication\-*<your\-region>*** policy\.

1. Choose **Policy Actions** and then choose **Delete**\.

1. In the navigation bar, choose **Roles**\.

1. Choose the **kinesis\-analytics\-MyApplication\-*<your\-region>*** role\.

1. Choose **Delete role** and then confirm the deletion\.

### Delete Your CloudWatch Resources<a name="examples-sliding-cleanup-cw"></a>

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. In the navigation bar, choose **Logs**\.

1. Choose the **/aws/kinesis\-analytics/MyApplication** log group\.

1. Choose **Delete Log Group** and then confirm the deletion\.