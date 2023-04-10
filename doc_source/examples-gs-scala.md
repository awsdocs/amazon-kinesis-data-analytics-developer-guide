# Getting Started \(Scala\)<a name="examples-gs-scala"></a>

**Note**  
Starting from version 1\.15 Flink is Scala free\. Applications can now use the Java API from any Scala version\. Flink still uses Scala in a few key components internally but doesn't expose Scala into the user code classloader\. Because of that, users need to add Scala dependencies into their jar\-archives\.  
For more information about Scala changes in Flink 1\.15, see [ Scala Free in One Fifteen](https://flink.apache.org/2022/02/22/scala-free.html)\.

In this exercise, you create a Kinesis Data Analytics application application for Scala with a Kinesis stream as a source and a sink\. 

**Topics**
+ [Create Dependent Resources](#examples-gs-scala-resources)
+ [Write Sample Records to the Input Stream](#examples-gs-scala-write)
+ [Download and Examine the Application Code](#examples-gs-scala-download)
+ [Compile and upload the application code](#examples-gs-scala-upload)
+ [Create and run the Application \(console\)](gs-scala-7.md)
+ [Create and run the application \(CLI\)](examples-gs-scala-create-run-cli.md)
+ [Clean Up AWS Resources](examples-gs-scala-cleanup.md)

## Create Dependent Resources<a name="examples-gs-scala-resources"></a>

Before you create a Kinesis Data Analytics application for this exercise, you create the following dependent resources: 
+ Two Kinesis streams for input and output\.
+ An Amazon S3 bucket to store the application's code \(`ka-app-code-<username>`\) 

You can create the Kinesis streams and Amazon S3 bucket using the console\. For instructions for creating these resources, see the following topics:
+ [Creating and Updating Data Streams](https://docs.aws.amazon.com/kinesis/latest/dev/amazon-kinesis-streams.html) in the *Amazon Kinesis Data Streams Developer Guide*\. Name your data streams **ExampleInputStream** and **ExampleOutputStream**\.

  To create the data streams \(AWS CLI\)
  + To create the first stream \(`ExampleInputStream`\), use the following Amazon Kinesis create\-stream AWS CLI command\.

    ```
    aws kinesis create-stream \
        --stream-name ExampleInputStream \
        --shard-count 1 \
        --region us-west-2 \
        --profile adminuser
    ```
  + To create the second stream that the application uses to write output, run the same command, changing the stream name to `ExampleOutputStream`\.

    ```
    aws kinesis create-stream \
        --stream-name ExampleOutputStream \
        --shard-count 1 \
        --region us-west-2 \
        --profile adminuser
    ```
+ [How Do I Create an S3 Bucket?](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/create-bucket.html) in the *Amazon Simple Storage Service User Guide*\. Give the Amazon S3 bucket a globally unique name by appending your login name, such as **ka\-app\-code\-*<username>***\.

**Other Resources**

When you create your application, Kinesis Data Analytics creates the following Amazon CloudWatch resources if they don't already exist:
+ A log group called `/aws/kinesis-analytics-java/MyApplication`
+ A log stream called `kinesis-analytics-log-stream`

## Write Sample Records to the Input Stream<a name="examples-gs-scala-write"></a>

In this section, you use a Python script to write sample records to the stream for the application to process\.

**Note**  
This section requires the [AWS SDK for Python \(Boto\)](https://aws.amazon.com/developers/getting-started/python/)\.

**Note**  
The Python script in this section uses the AWS CLI\. You must configure your AWS CLI to use your account credentials and default region\. To configure your AWS CLI, enter the following:  

```
aws configure
```

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

## Download and Examine the Application Code<a name="examples-gs-scala-download"></a>

The Python application code for this example is available from GitHub\. To download the application code, do the following:

1. Install the Git client if you haven't already\. For more information, see [Installing Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)\. 

1. Clone the remote repository with the following command:

   ```
   git clone https://github.com/aws-samples/amazon-kinesis-data-analytics-java-examples
   ```

1. Navigate to the `amazon-kinesis-data-analytics-java-examples/scala/GettingStarted` directory\.

Note the following about the application code:
+ A `build.sbt` file contains information about the application's configuration and dependencies, including the Kinesis Data Analytics libraries\.
+ The `BasicStreamingJob.scala` file contains the main method that defines the application's functionality\.
+ The application uses a Kinesis source to read from the source stream\. The following snippet creates the Kinesis source:

  ```
  private def createSource: FlinkKinesisConsumer[String] = {
    val applicationProperties = KinesisAnalyticsRuntime.getApplicationProperties
    val inputProperties = applicationProperties.get("ConsumerConfigProperties")
  
    new FlinkKinesisConsumer[String](inputProperties.getProperty(streamNameKey, defaultInputStreamName),
      new SimpleStringSchema, inputProperties)
  }
  ```

  The application also uses a Kinesis sink to write into the result stream\. The following snippet creates the Kinesis sink:

  ```
  private def createSink: KinesisStreamsSink[String] = {
    val applicationProperties = KinesisAnalyticsRuntime.getApplicationProperties
    val outputProperties = applicationProperties.get("ProducerConfigProperties")
  
    KinesisStreamsSink.builder[String]
      .setKinesisClientProperties(outputProperties)
      .setSerializationSchema(new SimpleStringSchema)
      .setStreamName(outputProperties.getProperty(streamNameKey, defaultOutputStreamName))
      .setPartitionKeyGenerator((element: String) => String.valueOf(element.hashCode))
      .build
  }
  ```
+ The application creates source and sink connectors to access external resources using a StreamExecutionEnvironment object\.
+ The application creates source and sink connectors using dynamic application properties\. Runtime application's properties are read to configure the connectors\. For more information about runtime properties, see [Runtime Properties](https://docs.aws.amazon.com/kinesisanalytics/latest/java/how-properties.html)\.

## Compile and upload the application code<a name="examples-gs-scala-upload"></a>

In this section, you compile and upload your application code to the Amazon S3 bucket you created in the [Create Dependent Resources](#examples-gs-scala-resources) section\.

**Compile the Application Code**

In this section, you use the [SBT](https://www.scala-sbt.org/) build tool to build the Scala code for the application\. To install SBT, see [Install sbt with cs setup](https://www.scala-sbt.org/download.html)\. You also need to install the Java Development Kit \(JDK\)\. See [Prerequisites for Completing the Exercises](https://docs.aws.amazon.com/kinesisanalytics/latest/java/getting-started.html#setting-up-prerequisites)\.

1. To use your application code, you compile and package it into a JAR file\. You can compile and package your code with SBT:

   ```
   sbt assembly
   ```

1. If the application compiles successfully, the following file is created:

   ```
   target/scala-3.2.0/getting-started-scala-1.0.jar
   ```

**Upload the Apache Flink Streaming Scala Code**

In this section, you create an Amazon S3 bucket and upload your application code\.

1. Open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. Choose **Create bucket**

1. Enter `ka-app-code-<username>` in the **Bucket name** field\. Add a suffix to the bucket name, such as your user name, to make it globally unique\. Choose **Next**\.

1. In **Configure options**, keep the settings as they are, and choose **Next**\.

1. In **Set permissions**, keep the settings as they are, and choose **Next**\.

1. Choose **Create bucket**\.

1. Choose the `ka-app-code-<username>` bucket, and then choose **Upload**\.

1. In the **Select files** step, choose **Add files**\. Navigate to the `getting-started-scala-1.0.jar` file that you created in the previous step\. 

1. You don't need to change any of the settings for the object, so choose **Upload**\.

Your application code is now stored in an Amazon S3 bucket where your application can access it\.