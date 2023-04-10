# Example: Read From a Kinesis Stream in a Different Account<a name="examples-cross"></a>

This example demonstrates how to create an Amazon Kinesis Data Analytics application that reads data from a Kinesis stream in a different account\. In this example, you will use one account for the source Kinesis stream, and a second account for the Kinesis Data Analytics application and sink Kinesis stream\.

**Topics**
+ [Prerequisites](#examples-cross-prerequisites)
+ [Setup](#examples-cross-setup)
+ [Create Source Kinesis Stream](#examples-cross-streams)
+ [Create and Update IAM Roles and Policies](#examples-cross-iam)
+ [Update the Python Script](#examples-cross-python)
+ [Update the Java Application](#examples-cross-app)
+ [Build, Upload, and Run the Application](#examples-cross-run)

## Prerequisites<a name="examples-cross-prerequisites"></a>
+ In this tutorial, you modify the *Getting Started* example to read data from a Kinesis stream in a different account\. Complete the [Getting Started \(DataStream API\)](getting-started.md) tutorial before proceeding\.
+ You need two AWS accounts to complete this tutorial: one for the source stream, and one for the application and the sink stream\. Use the AWS account you used for the Getting Started tutorial for the application and sink stream\. Use a different AWS account for the source stream\.

## Setup<a name="examples-cross-setup"></a>

You will access your two AWS accounts by using named profiles\. Modify your AWS credentials and configuration files to include two profiles that contain the region and connection information for your two accounts\.

The following example credential file contains two named profiles, `ka-source-stream-account-profile` and `ka-sink-stream-account-profile`\. Use the account you used for the Getting Started tutorial for the sink stream account\.

```
[ka-source-stream-account-profile]
aws_access_key_id=AKIAIOSFODNN7EXAMPLE
aws_secret_access_key=wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY

[ka-sink-stream-account-profile]
aws_access_key_id=AKIAI44QH8DHBEXAMPLE
aws_secret_access_key=je7MtGbClwBF/2Zp9Utk/h3yCo8nvbEXAMPLEKEY
```

The following example configuration file contains the same named profiles with region and output format information\.

```
[profile ka-source-stream-account-profile]
region=us-west-2
output=json

[profile ka-sink-stream-account-profile]
region=us-west-2
output=json
```

**Note**  
This tutorial does not use the `ka-sink-stream-account-profile`\. It is included as an example of how to access two different AWS accounts using profiles\.

For more information on using named profiles with the AWS CLI, see [Named Profiles](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-profiles.html) in the *AWS Command Line Interface* documentation\.

## Create Source Kinesis Stream<a name="examples-cross-streams"></a>

In this section, you will create the Kinesis stream in the source account\.

Enter the following command to create the Kinesis stream that the application will use for input\. Note that the `--profile` parameter specifies which account profile to use\.

```
$ aws kinesis create-stream \
--stream-name SourceAccountExampleInputStream \
--shard-count 1 \
--profile ka-source-stream-account-profile
```

## Create and Update IAM Roles and Policies<a name="examples-cross-iam"></a>

To allow object access across AWS accounts, you create an IAM role and policy in the source account\. Then, you modify the IAM policy in the sink account\. For information about creating IAM roles and policies, see the following topics in the *AWS Identity and Access Management User Guide*:
+ [Creating IAM Roles](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_create.html)
+ [Creating IAM Policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_create.html)

### Sink Account Roles and Policies<a name="examples-cross-iam-sink"></a>

1. Edit the `kinesis-analytics-service-MyApplication-us-west-2` policy from the Getting Started tutorial\. This policy allows the role in the source account to be assumed in order to read the source stream\. 
**Note**  
When you use the console to create your application, the console creates a policy called `kinesis-analytics-service-<application name>-<application region>`, and a role called `kinesis-analytics-<application name>-<application region>`\. 

   Add the highlighted section below to the policy\. Replace the sample account ID \(*SOURCE01234567*\) with the ID of the account you will use for the source stream\.

   ```
   {
       "Version": "2012-10-17",
       "Statement": [
           {
               "Sid": "AssumeRoleInSourceAccount",
               "Effect": "Allow",
               "Action": "sts:AssumeRole",
               "Resource": "arn:aws:iam::SOURCE01234567:role/KA-Source-Stream-Role"
           },
           {
               "Sid": "ReadCode",
               "Effect": "Allow",
               "Action": [
                   "s3:GetObject",
                   "s3:GetObjectVersion"
               ],
               "Resource": [
                   "arn:aws:s3:::ka-app-code-username/aws-kinesis-analytics-java-apps-1.0.jar"
               ]
           },
           {
               "Sid": "ListCloudwatchLogGroups",
               "Effect": "Allow",
               "Action": [
                   "logs:DescribeLogGroups"
               ],
               "Resource": [
                   "arn:aws:logs:us-west-2:SINK012345678:log-group:*"
               ]
           },
           {
               "Sid": "ListCloudwatchLogStreams",
               "Effect": "Allow",
               "Action": [
                   "logs:DescribeLogStreams"
               ],
               "Resource": [
                   "arn:aws:logs:us-west-2:SINK012345678:log-group:/aws/kinesis-analytics/MyApplication:log-stream:*"
               ]
           },
           {
               "Sid": "PutCloudwatchLogs",
               "Effect": "Allow",
               "Action": [
                   "logs:PutLogEvents"
               ],
               "Resource": [
                   "arn:aws:logs:us-west-2:SINK012345678:log-group:/aws/kinesis-analytics/MyApplication:log-stream:kinesis-analytics-log-stream"
               ]
           }
       ]
   }
   ```

1. Open the `kinesis-analytics-MyApplication-us-west-2` role, and make a note of its Amazon Resource Name \(ARN\)\. You will need it in the next section\. The role ARN looks like the following\.

   ```
   arn:aws:iam::SINK012345678:role/service-role/kinesis-analytics-MyApplication-us-west-2
   ```

### Source Account Roles and Policies<a name="examples-cross-iam-source"></a>

1. Create a policy in the source account called `KA-Source-Stream-Policy`\. Use the following JSON for the policy\. Replace the sample account number with the account number of the source account\.

   ```
   {
       "Version": "2012-10-17",
       "Statement": [
           {
               "Sid": "ReadInputStream",
               "Effect": "Allow",
               "Action": [
                   "kinesis:DescribeStream",
                   "kinesis:GetRecords",
                   "kinesis:GetShardIterator",
                   "kinesis:ListShards"
               ],
               "Resource": 
                  "arn:aws:kinesis:us-west-2:SOURCE123456784:stream/SourceAccountExampleInputStream"
           }
       ]
   }
   ```

1. Create a role in the source account called `KA-Source-Stream-Role`\. Do the following to create the role using the **Kinesis Analytics** use case:

   1. In the IAM Management Console, choose **Create Role**\.

   1. On the **Create Role** page, choose **AWS Service**\. In the service list, choose **Kinesis**\.

   1. In the **Select your use case** section, choose **Kinesis Analytics**\.

   1. Choose **Next: Permissions**\.

   1. Add the `KA-Source-Stream-Policy` permissions policy you created in the previous step\. Choose **Next:Tags**\.

   1. Choose **Next: Review**\.

   1. Name the role `KA-Source-Stream-Role`\. Your application will use this role to access the source stream\.

1. Add the `kinesis-analytics-MyApplication-us-west-2` ARN from the sink account to the trust relationship of the `KA-Source-Stream-Role` role in the source account:

   1. Open the `KA-Source-Stream-Role` in the IAM console\.

   1. Choose the **Trust Relationships** tab\.

   1. Choose **Edit trust relationship**\.

   1. Use the following code for the trust relationship\. Replace the sample account ID \(**SINK012345678**\) with your sink account ID\.

      ```
      {
          "Version": "2012-10-17",
          "Statement": [
            {
              "Effect": "Allow",
              "Principal": {
                "AWS": "arn:aws:iam::SINK012345678:role/service-role/kinesis-analytics-MyApplication-us-west-2"
              },
              "Action": "sts:AssumeRole"
            }
          ]
      }
      ```

## Update the Python Script<a name="examples-cross-python"></a>

In this section, you update the Python script that generates sample data to use the source account profile\.

Update the `stock.py` script with the following highlighted changes\.

```
import json
import boto3
import random
import datetime
import os

os.environ['AWS_PROFILE'] ='ka-source-stream-account-profile'
os.environ['AWS_DEFAULT_REGION'] = 'us-west-2'


kinesis = boto3.client('kinesis')
def getReferrer():
    data = {}
    now = datetime.datetime.now()
    str_now = now.isoformat()
    data['event_time'] = str_now
    data['ticker'] = random.choice(['AAPL', 'AMZN', 'MSFT', 'INTC', 'TBV'])
    price = random.random() * 100
    data['price'] = round(price, 2)
    return data

while True:
        data = json.dumps(getReferrer())
        print(data)
        kinesis.put_record(
                StreamName="SourceAccountExampleInputStream",
                Data=data,
                PartitionKey="partitionkey")
```

## Update the Java Application<a name="examples-cross-app"></a>

In this section, you update the Java application code to assume the source account role when reading from the source stream\.

Make the following changes to the `BasicStreamingJob.java` file\. Replace the example source account number \(*SOURCE01234567*\) with your source account number\.

```
package com.amazonaws.services.kinesisanalytics;

import com.amazonaws.services.kinesisanalytics.runtime.KinesisAnalyticsRuntime;
import org.apache.flink.api.common.serialization.SimpleStringSchema;
import org.apache.flink.streaming.api.datastream.DataStream;
import org.apache.flink.streaming.api.environment.StreamExecutionEnvironment;
import org.apache.flink.streaming.connectors.kinesis.FlinkKinesisConsumer;
import org.apache.flink.streaming.connectors.kinesis.FlinkKinesisProducer;
import org.apache.flink.streaming.connectors.kinesis.config.ConsumerConfigConstants;
import org.apache.flink.streaming.connectors.kinesis.config.AWSConfigConstants;

import java.io.IOException;
import java.util.Map;
import java.util.Properties;

 /**
 * A basic Kinesis Data Analytics for Java application with Kinesis data streams
 * as source and sink.
 */
public class BasicStreamingJob {
    private static final String region = "us-west-2";
    private static final String inputStreamName = "SourceAccountExampleInputStream";
    private static final String outputStreamName = ExampleOutputStream;
    private static final String roleArn = "arn:aws:iam::SOURCE01234567:role/KA-Source-Stream-Role";
    private static final String roleSessionName = "ksassumedrolesession";

    private static DataStream<String> createSourceFromStaticConfig(StreamExecutionEnvironment env) {
        Properties inputProperties = new Properties();
        inputProperties.setProperty(AWSConfigConstants.AWS_CREDENTIALS_PROVIDER, "ASSUME_ROLE");
        inputProperties.setProperty(AWSConfigConstants.AWS_ROLE_ARN, roleArn);
        inputProperties.setProperty(AWSConfigConstants.AWS_ROLE_SESSION_NAME, roleSessionName);
        inputProperties.setProperty(ConsumerConfigConstants.AWS_REGION, region);
        inputProperties.setProperty(ConsumerConfigConstants.STREAM_INITIAL_POSITION, "LATEST");

        return env.addSource(new FlinkKinesisConsumer<>(inputStreamName, new SimpleStringSchema(), inputProperties));
    }

    private static KinesisStreamsSink<String> createSinkFromStaticConfig() {
        Properties outputProperties = new Properties();
        outputProperties.setProperty(AWSConfigConstants.AWS_REGION, region);

        return KinesisStreamsSink.<String>builder()
                .setKinesisClientProperties(outputProperties)
                .setSerializationSchema(new SimpleStringSchema())
                .setStreamName(outputProperties.getProperty("OUTPUT_STREAM", "ExampleOutputStream"))
                .setPartitionKeyGenerator(element -> String.valueOf(element.hashCode()))
                .build();
    }

    public static void main(String[] args) throws Exception {
        // set up the streaming execution environment
        final StreamExecutionEnvironment env = StreamExecutionEnvironment.getExecutionEnvironment();

        DataStream<String> input = createSourceFromStaticConfig(env);

        input.addSink(createSinkFromStaticConfig());

        env.execute("Flink Streaming Java API Skeleton");
    }
}
```

## Build, Upload, and Run the Application<a name="examples-cross-run"></a>

Do the following to update and run the application:

1. Build the application again by running the following command in the directory with the `pom.xml` file\.

   ```
   mvn package -Dflink.version=1.15.3
   ```

1. Delete the previous JAR file from your Amazon Simple Storage Service \(Amazon S3\) bucket, and then upload the new `aws-kinesis-analytics-java-apps-1.0.jar` file to the S3 bucket\.

1. In the application's page in the Kinesis Data Analytics console, choose **Configure**, **Update** to reload the application JAR file\.

1. Run the `stock.py` script to send data to the source stream\.

   ```
   python stock.py
   ```

The application now reads data from the Kinesis stream in the other account\.

You can verify that the application is working by checking the `PutRecords.Bytes` metric of the `ExampleOutputStream` stream\. If there is activity in the output stream, the application is functioning properly\.