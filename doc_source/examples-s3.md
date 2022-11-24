# Example: Writing to an Amazon S3 Bucket<a name="examples-s3"></a>

In this exercise, you create a Kinesis Data Analytics for Apache Flink application that has a Kinesis data stream as a source and an Amazon S3 bucket as a sink\. Using the sink, you can verify the output of the application in the Amazon S3 console\. 

**Note**  
To set up required prerequisites for this exercise, first complete the [Getting Started \(DataStream API\)](getting-started.md) exercise\.

**Topics**
+ [Create Dependent Resources](#examples-s3-resources)
+ [Write Sample Records to the Input Stream](#examples-s3-write)
+ [Download and Examine the Application Code](#examples-s3-download)
+ [Modify the Application Code](#examples-s3-modify)
+ [Compile the Application Code](#examples-s3-compile)
+ [Upload the Apache Flink Streaming Java Code](#examples-s3-upload)
+ [Create and Run the Kinesis Data Analytics Application](#examples-s3-create-run)
+ [Verify the Application Output](#examples-s3-verify)
+ [Optional: Customize the Source and Sink](#examples-s3-customize)
+ [Clean Up AWS Resources](#examples-s3-cleanup)

## Create Dependent Resources<a name="examples-s3-resources"></a>

Before you create a Kinesis Data Analytics for Apache Flink application for this exercise, you create the following dependent resources: 
+ A Kinesis data stream \(`ExampleInputStream`\)\.
+ An Amazon S3 bucket to store the application's code and output \(`ka-app-<username>`\) 

**Note**  
Kinesis Data Analytics for Apache Flink cannot write data to Amazon S3 with server\-side encryption enabled on Kinesis Data Analytics\.

You can create the Kinesis stream and Amazon S3 bucket using the console\. For instructions for creating these resources, see the following topics:
+ [Creating and Updating Data Streams](https://docs.aws.amazon.com/kinesis/latest/dev/amazon-kinesis-streams.html) in the *Amazon Kinesis Data Streams Developer Guide*\. Name your data stream **ExampleInputStream**\.
+ [How Do I Create an S3 Bucket?](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/create-bucket.html) in the *Amazon Simple Storage Service User Guide*\. Give the Amazon S3 bucket a globally unique name by appending your login name, such as **ka\-app\-*<username>***\. Create two folders \(**code** and **data**\) in the Amazon S3 bucket\.

The application creates the following CloudWatch resources if they don't already exist:
+ A log group called `/aws/kinesis-analytics-java/MyApplication`\.
+ A log stream called `kinesis-analytics-log-stream`\.

## Write Sample Records to the Input Stream<a name="examples-s3-write"></a>

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

## Download and Examine the Application Code<a name="examples-s3-download"></a>

The Java application code for this example is available from GitHub\. To download the application code, do the following:

1. Install the Git client if you haven't already\. For more information, see [Installing Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)\. 

1. Clone the remote repository with the following command:

   ```
   git clone https://github.com/aws-samples/amazon-kinesis-data-analytics-java-examples
   ```

1. Navigate to the `amazon-kinesis-data-analytics-java-examples/S3Sink` directory\.

The application code is located in the `S3StreamingSinkJob.java` file\. Note the following about the application code:
+ The application uses a Kinesis source to read from the source stream\. The following snippet creates the Kinesis source:

  ```
  return env.addSource(new FlinkKinesisConsumer<>(inputStreamName,
                  new SimpleStringSchema(), inputProperties));
  ```
+ You need to add the following import statement:

  ```
  import org.apache.flink.streaming.api.windowing.assigners.TumblingProcessingTimeWindows;
  ```
+ The application uses an Apache Flink S3 sink to write to Amazon S3\. 

  The sink reads messages in a tumbling window, encodes messages into S3 bucket objects, and sends the encoded objects to the S3 sink\. The following code encodes objects for sending to Amazon S3:

  ```
  input.map(value -> { // Parse the JSON
              JsonNode jsonNode = jsonParser.readValue(value, JsonNode.class);
              return new Tuple2><jsonNode.get("TICKER").toString(), 1);
          }).returns(Types.TUPLE(Types.STRING, Types.INT))
                  .keyBy(0) // Logically partition the stream for each word
                  // .timeWindow(Time.minutes(1)) // Tumbling window definition // Flink 1.11
                  .window(TumblingProcessingTimeWindows.of(Time.minutes(1))) // Flink 1.13
                  .sum(1) // Count the appearances by ticker per partition
                  .map(value -> value.f0 + " count: " + value.f1.toString() + "\n")
                  .addSink(createS3SinkFromStaticConfig());
  
          env.execute("Flink S3 Streaming Sink Job");
  ```

**Note**  
The application uses a Flink `StreamingFileSink` object to write to Amazon S3\. For more information about the `StreamingFileSink`, see [StreamingFileSink](https://ci.apache.org/projects/flink/flink-docs-release-1.11/dev/connectors/streamfile_sink.html) in the [Apache Flink documentation](https://ci.apache.org/projects/flink/flink-docs-release-1.11/)\.

## Modify the Application Code<a name="examples-s3-modify"></a>

In this section, you modify the application code to write output to your Amazon S3 bucket\.

Update the following line with your user name to specify the application's output location:

```
private static final String s3SinkPath = "s3a://ka-app-<username>/data";
```

## Compile the Application Code<a name="examples-s3-compile"></a>

To compile the application, do the following:

1. Install Java and Maven if you haven't already\. For more information, see [Prerequisites](getting-started.md#setting-up-prerequisites) in the [Getting Started \(DataStream API\)](getting-started.md) tutorial\.

1. Compile the application with the following command: 

   ```
   mvn package -Dflink.version=1.13.2
   ```

Compiling the application creates the application JAR file \(`target/aws-kinesis-analytics-java-apps-1.0.jar`\)\.

**Note**  
The provided source code relies on libraries from Java 11\. If you are using a development environment, 

## Upload the Apache Flink Streaming Java Code<a name="examples-s3-upload"></a>

In this section, you upload your application code to the Amazon S3 bucket you created in the [Create Dependent ResourcesWrite Sample Records to the Input Stream](#examples-s3-resources) section\.

1. In the Amazon S3 console, choose the **ka\-app\-*<username>*** bucket, navigate to the **code** folder, and choose **Upload**\.

1. In the **Select files** step, choose **Add files**\. Navigate to the `aws-kinesis-analytics-java-apps-1.0.jar` file that you created in the previous step\. 

1. You don't need to change any of the settings for the object, so choose **Upload**\.

Your application code is now stored in an Amazon S3 bucket where your application can access it\.

## Create and Run the Kinesis Data Analytics Application<a name="examples-s3-create-run"></a>

Follow these steps to create, configure, update, and run the application using the console\.

### Create the Application<a name="examples-s3-create"></a>

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
For **Application name**, enter **MyApplication**\.
For **Runtime**, choose **Apache Flink**\.
Leave the version as **Apache Flink version 1\.13\.2 \(Recommended version\)**\.

1. For **Access permissions**, choose **Create / update IAM role `kinesis-analytics-MyApplication-us-west-2`**\.

1. Choose **Create application**\.

**Note**  
When you create a Kinesis Data Analytics for Apache Flink application using the console, you have the option of having an IAM role and policy created for your application\. Your application uses this role and policy to access its dependent resources\. These IAM resources are named using your application name and Region as follows:  
Policy: `kinesis-analytics-service-MyApplication-us-west-2`
Role: `kinesis-analytics-MyApplication-us-west-2`

### Edit the IAM Policy<a name="get-started-exercise-7-console-iam"></a>

Edit the IAM policy to add permissions to access the Kinesis data stream\.

1. Open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. Choose **Policies**\. Choose the **`kinesis-analytics-service-MyApplication-us-west-2`** policy that the console created for you in the previous section\. 

1. On the **Summary** page, choose **Edit policy**\. Choose the **JSON** tab\.

1. Add the highlighted section of the following policy example to the policy\. Replace the sample account IDs \(*012345678901*\) with your account ID\. Replace <username> with your user name\.

   ```
   {
               "Sid": "ReadCode",
               "Effect": "Allow",
               "Action": [
                   "s3:GetObject",
                   "s3:GetObjectVersion"
               ],
               "Resource": [
                   "arn:aws:s3:::kinesis-analytics-placeholder-s3-bucket/kinesis-analytics-placeholder-s3-object"
               ]
           },
           {
               "Sid": "ListCloudwatchLogGroups",
               "Effect": "Allow",
               "Action": [
                   "logs:DescribeLogGroups"
               ],
               "Resource": [
                   "arn:aws:logs:region:account-id:log-group:*"
               ]
           },
           {
               "Sid": "ListCloudwatchLogStreams",
               "Effect": "Allow",
               "Action": [
                   "logs:DescribeLogStreams"
               ],
               "Resource": [
                   "arn:aws:logs:region:account-id:log-group:%LOG_GROUP_PLACEHOLDER%:log-stream:*"
               ]
           },
           {
               "Sid": "PutCloudwatchLogs",
               "Effect": "Allow",
               "Action": [
                   "logs:PutLogEvents"
               ],
               "Resource": [
                   "arn:aws:logs:region:account-id:log-group:%LOG_GROUP_PLACEHOLDER%:log-stream:%LOG_STREAM_PLACEHOLDER%"
               ]
           }
           ,
           {
               "Sid": "ReadInputStream",
               "Effect": "Allow",
               "Action": "kinesis:*",
               "Resource": "arn:aws:kinesis:us-west-2:012345678901:stream/ExampleInputStream"
           },
           {
               "Sid": "WriteObjects",
               "Effect": "Allow",
               "Action": [
                   "s3:Abort*",
                   "s3:DeleteObject*",
                   "s3:GetObject*",
                   "s3:GetBucket*",
                   "s3:List*",
                   "s3:ListBucket",
                   "s3:PutObject"
               ],
               "Resource": [
                   "arn:aws:s3:::ka-app-<username>",
                   "arn:aws:s3:::ka-app-<username>/*"
               ]
           }
       ]
   }
   ```

### Configure the Application<a name="examples-s3-configure"></a>

1. On the **MyApplication** page, choose **Configure**\.

1. On the **Configure application** page, provide the **Code location**:
   + For **Amazon S3 bucket**, enter **ka\-app\-*<username>***\.
   + For **Path to Amazon S3 object**, enter **code/aws\-kinesis\-analytics\-java\-apps\-1\.0\.jar**\.

1. Under **Access to application resources**, for **Access permissions**, choose **Create / update IAM role `kinesis-analytics-MyApplication-us-west-2`**\.

1. Under **Monitoring**, ensure that the **Monitoring metrics level** is set to **Application**\.

1. For **CloudWatch logging**, select the **Enable** check box\.

1. Choose **Update**\.

**Note**  
When you choose to enable CloudWatch logging, Kinesis Data Analytics creates a log group and log stream for you\. The names of these resources are as follows:   
Log group: `/aws/kinesis-analytics/MyApplication`
Log stream: `kinesis-analytics-log-stream`
This log stream is used to monitor the application\. This is not the same log stream that the application uses to send results\.

### Run the Application<a name="examples-s3-run"></a>

1. On the **MyApplication** page, choose **Run**\. Leave the **Run without snapshot** option selected, and confirm the action\.

1. When the application is running, refresh the page\. The console shows the **Application graph**\.

## Verify the Application Output<a name="examples-s3-verify"></a>

In the Amazon S3 console, open the **data** folder in your S3 bucket\.

After a few minutes, objects containing aggregated data from the application will appear\.

## Optional: Customize the Source and Sink<a name="examples-s3-customize"></a>

In this section, you customize settings on the source and sink objects\.

**Note**  
After changing the code sections described in the sections following, do the following to reload the application code:  
Repeat the steps in the [Compile the Application Code](#examples-s3-compile) section to compile the updated application code\.
Repeat the steps in the [Upload the Apache Flink Streaming Java Code](#examples-s3-upload) section to upload the updated application code\.
On the application's page in the console, choose **Configure** and then choose **Update** to reload the updated application code into your application\.

**Topics**
+ [Configure Data Partitioning](#examples-s3-configure-partition)
+ [Configure Read Frequency](#examples-s3-configure-read)
+ [Configure Write Buffering](#examples-s3-configure-write)

### Configure Data Partitioning<a name="examples-s3-configure-partition"></a>

In this section, you configure the names of the folders that the streaming file sink creates in the S3 bucket\. You do this by adding a bucket assigner to the streaming file sink\.

To customize the folder names created in the S3 bucket, do the following:

1. Add the following import statements to the beginning of the `S3StreamingSinkJob.java` file:

   ```
   import org.apache.flink.streaming.api.functions.sink.filesystem.rollingpolicies.DefaultRollingPolicy;
   import org.apache.flink.streaming.api.functions.sink.filesystem.bucketassigners.DateTimeBucketAssigner;
   ```

1. Update the `createS3SinkFromStaticConfig()` method in the code to look like the following:

   ```
   private static StreamingFileSink<String> createS3SinkFromStaticConfig() {
   
       final StreamingFileSink<String> sink = StreamingFileSink
           .forRowFormat(new Path(s3SinkPath), new SimpleStringEncoder<String>("UTF-8"))
           .withBucketAssigner(new DateTimeBucketAssigner("yyyy-MM-dd--HH"))
           .withRollingPolicy(DefaultRollingPolicy.create().build())
           .build();
       return sink;
   }
   ```

The preceding code example uses the `DateTimeBucketAssigner` with a custom date format to create folders in the S3 bucket\. The `DateTimeBucketAssigner` uses the current system time to create bucket names\. If you want to create a custom bucket assigner to further customize the created folder names, you can create a class that implements [BucketAssigner](https://ci.apache.org/projects/flink/flink-docs-release-1.11/api/java/org/apache/flink/streaming/api/functions/sink/filesystem/BucketAssigner.html)\. You implement your custom logic by using the `getBucketId` method\.

A custom implementation of `BucketAssigner` can use the [Context](https://ci.apache.org/projects/flink/flink-docs-release-1.11/api/java/org/apache/flink/streaming/api/functions/sink/filesystem/BucketAssigner.Context.html) parameter to obtain more information about a record in order to determine its destination folder\.

### Configure Read Frequency<a name="examples-s3-configure-read"></a>

In this section, you configure the frequency of reads on the source stream\.

The Kinesis Streams consumer reads from the source stream five times per second by default\. This frequency will cause issues if there is more than one client reading from the stream, or if the application needs to retry reading a record\. You can avoid these issues by setting the read frequency of the consumer\.

To set the read frequency of the Kinesis consumer, you set the `SHARD_GETRECORDS_INTERVAL_MILLIS` setting\.

The following code example sets the `SHARD_GETRECORDS_INTERVAL_MILLIS` setting to one second:

```
kinesisConsumerConfig.setProperty(ConsumerConfigConstants.SHARD_GETRECORDS_INTERVAL_MILLIS, "1000");
```

### Configure Write Buffering<a name="examples-s3-configure-write"></a>

In this section, you configure the write frequency and other settings of the sink\.

By default, the application writes to the destination bucket every minute\. You can change this interval and other settings by configuring the `DefaultRollingPolicy` object\.

**Note**  
The Apache Flink streaming file sink writes to its output bucket every time the application creates a checkpoint\. The application creates a checkpoint every minute by default\. To increase the write interval of the S3 sink, you must also increase the checkpoint interval\.

To configure the `DefaultRollingPolicy` object, do the following:

1. Increase the application's `CheckpointInterval` setting\. The following input for the [ UpdateApplication](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_UpdateApplication.html) action sets the checkpoint interval to 10 minutes:

   ```
   {
      "ApplicationConfigurationUpdate": {  
         "FlinkApplicationConfigurationUpdate": { 
            "CheckpointConfigurationUpdate": {
               "ConfigurationTypeUpdate" : "CUSTOM", 
               "CheckpointIntervalUpdate": 600000
            }
         }
      },
      "ApplicationName": "MyApplication",
      "CurrentApplicationVersionId": 5
   }
   ```

   To use the preceding code, specify the current application version\. You can retrieve the application version by using the [ListApplications](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_ListApplications.html) action\.

1. Add the following import statement to the beginning of the `S3StreamingSinkJob.java` file:

   ```
   import java.util.concurrent.TimeUnit; 
   ```

1. Update the `createS3SinkFromStaticConfig` method in the `S3StreamingSinkJob.java` file to look like the following:

   ```
   private static StreamingFileSink<String> createS3SinkFromStaticConfig() {
   
           final StreamingFileSink<String> sink = StreamingFileSink
                   .forRowFormat(new Path(s3SinkPath), new SimpleStringEncoder<String>("UTF-8"))
                   .withBucketAssigner(new DateTimeBucketAssigner("yyyy-MM-dd--HH"))
                   .withRollingPolicy(
                           DefaultRollingPolicy.create()
                               .withRolloverInterval(TimeUnit.MINUTES.toMillis(8))
                               .withInactivityInterval(TimeUnit.MINUTES.toMillis(5))
                               .withMaxPartSize(1024 * 1024 * 1024)
                               .build())
                   .build();
           return sink;
       }
   ```

   The preceding code example sets the frequency of writes to the Amazon S3 bucket to 8 minutes\.

For more information about configuring the Apache Flink streaming file sink, see [Row\-encoded Formats](https://ci.apache.org/projects/flink/flink-docs-release-1.11/dev/connectors/streamfile_sink.html#row-encoded-formats) in the [Apache Flink documentation](https://ci.apache.org/projects/flink/flink-docs-release-1.11/)\.

## Clean Up AWS Resources<a name="examples-s3-cleanup"></a>

This section includes procedures for cleaning up AWS resources that you created in the Amazon S3 tutorial\.

**Topics**
+ [Delete Your Kinesis Data Analytics Application](#examples-s3-cleanup-app)
+ [Delete Your Kinesis Data Stream](#examples-s3-cleanup-stream)
+ [Delete Your Amazon S3 Objects and Bucket](#examples-s3-cleanup-s3)
+ [Delete Your IAM Resources](#examples-s3-cleanup-iam)
+ [Delete Your CloudWatch Resources](#examples-s3-cleanup-cw)

### Delete Your Kinesis Data Analytics Application<a name="examples-s3-cleanup-app"></a>

1. Open the Kinesis Data Analytics console at [ https://console\.aws\.amazon\.com/kinesisanalytics](https://console.aws.amazon.com/kinesisanalytics)\.

1. In the Kinesis Data Analytics panel, choose **MyApplication**\.

1. On the application's page, choose **Delete** and then confirm the deletion\.

### Delete Your Kinesis Data Stream<a name="examples-s3-cleanup-stream"></a>

1. Open the Kinesis console at [https://console\.aws\.amazon\.com/kinesis](https://console.aws.amazon.com/kinesis)\.

1. In the Kinesis Data Streams panel, choose **ExampleInputStream**\.

1. On the **ExampleInputStream** page, choose **Delete Kinesis Stream** and then confirm the deletion\.

### Delete Your Amazon S3 Objects and Bucket<a name="examples-s3-cleanup-s3"></a>

1. Open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. Choose the **ka\-app\-*<username>* bucket\.**

1. Choose **Delete** and then enter the bucket name to confirm deletion\.

### Delete Your IAM Resources<a name="examples-s3-cleanup-iam"></a>

1. Open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. On the navigation bar, choose **Policies**\.

1. In the filter control, enter **kinesis**\.

1. Choose the **kinesis\-analytics\-service\-MyApplication\-*<your\-region>*** policy\.

1. Choose **Policy Actions** and then choose **Delete**\.

1. On the navigation bar, choose **Roles**\.

1. Choose the **kinesis\-analytics\-MyApplication\-*<your\-region>*** role\.

1. Choose **Delete role** and then confirm the deletion\.

### Delete Your CloudWatch Resources<a name="examples-s3-cleanup-cw"></a>

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. On the navigation bar, choose **Logs**\.

1. Choose the **/aws/kinesis\-analytics/MyApplication** log group\.

1. Choose **Delete Log Group** and then confirm the deletion\.