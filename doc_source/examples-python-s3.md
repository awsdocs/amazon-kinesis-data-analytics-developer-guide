# Example: Send Streaming Data to Amazon S3 in Python<a name="examples-python-s3"></a>

In this exercise, you create a Python Kinesis Data Analytics application that streams data to an Amazon Simple Storage Service sink\. 

**Note**  
To set up required prerequisites for this exercise, first complete the [Getting Started \(Python\)](gs-python.md) exercise\.

**Topics**
+ [Create Dependent Resources](#examples-python-s3-resources)
+ [Write Sample Records to the Input Stream](#examples-python-s3-write)
+ [Download and Examine the Application Code](#examples-python-s3-download)
+ [Compress and Upload the Apache Flink Streaming Python Code](#examples-python-s3-upload)
+ [Create and Run the Kinesis Data Analytics Application](#examples-python-s3-create-run)
+ [Clean Up AWS Resources](#examples-python-s3-cleanup)

## Create Dependent Resources<a name="examples-python-s3-resources"></a>

Before you create a Kinesis Data Analytics application for this exercise, you create the following dependent resources: 
+ A Kinesis data stream \(`ExampleInputStream`\)
+ An Amazon S3 bucket to store the application's code and output \(`ka-app-code-<username>`\) 

You can create the Kinesis stream and Amazon S3 bucket using the console\. For instructions for creating these resources, see the following topics:
+ [Creating and Updating Data Streams](https://docs.aws.amazon.com/kinesis/latest/dev/amazon-kinesis-streams.html) in the *Amazon Kinesis Data Streams Developer Guide*\. Name your data stream **ExampleInputStream**\.
+ [How Do I Create an S3 Bucket?](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/create-bucket.html) in the *Amazon Simple Storage Service User Guide*\. Give the Amazon S3 bucket a globally unique name by appending your login name, such as **ka\-app\-code\-*<username>***\.

## Write Sample Records to the Input Stream<a name="examples-python-s3-write"></a>

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

## Download and Examine the Application Code<a name="examples-python-s3-download"></a>

The Python application code for this example is available from GitHub\. To download the application code, do the following:

1. Install the Git client if you haven't already\. For more information, see [Installing Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)\. 

1. Clone the remote repository with the following command:

   ```
   git clone https://github.com/aws-samples/>amazon-kinesis-data-analytics-java-examples
   ```

1. Navigate to the `amazon-kinesis-data-analytics-java-examples/python/S3Sink` directory\.

The application code is located in the `streaming-file-sink.py` file\. Note the following about the application code:
+ The application uses a Kinesis table source to read from the source stream\. The following snippet calls the `create_table` function to create the Kinesis table source:

  ```
  table_env.execute_sql(
          create_table(input_table_name, input_stream, input_region, stream_initpos)
      )
  ```

  The `create_table` function uses a SQL command to create a table that is backed by the streaming source:

  ```
  def create_table(table_name, stream_name, region, stream_initpos):
      return """ CREATE TABLE {0} (
                  ticker VARCHAR(6),
                  price DOUBLE,
                  event_time TIMESTAMP(3),
                  WATERMARK FOR event_time AS event_time - INTERVAL '5' SECOND
  
                )
                PARTITIONED BY (ticker)
                WITH (
                  'connector' = 'kinesis',
                  'stream' = '{1}',
                  'aws.region' = '{2}',
                  'scan.stream.initpos' = '{3}',
                  'sink.partitioner-field-delimiter' = ';',
                  'sink.producer.collection-max-count' = '100',
                  'format' = 'json',
                  'json.timestamp-format.standard' = 'ISO-8601'
                ) """.format(
          table_name, stream_name, region, stream_initpos
      )
  ```
+ The application uses the `filesystem` connector to send records to an Amazon S3 bucket:

  ```
   def create_sink_table(table_name, bucket_name):
      return """ CREATE TABLE {0} (
                  ticker VARCHAR(6),
                  price DOUBLE,
                  event_time TIMESTAMP(3),
                  WATERMARK FOR event_time AS event_time - INTERVAL '5' SECOND
  
                )
                PARTITIONED BY (ticker)
                WITH (
                    'connector'='filesystem',
                    'path'='s3a://{1}/',
                    'format'='csv',
                    'sink.partition-commit.policy.kind'='success-file',
                    'sink.partition-commit.delay' = '1 min'
                ) """.format(
          table_name, bucket_name)
  ```
+ The application uses the Kinesis Flink connector, from the `amazon-kinesis-connector-flink-2.0.0.jar` file\.

## Compress and Upload the Apache Flink Streaming Python Code<a name="examples-python-s3-upload"></a>

In this section, you upload your application code to the Amazon S3 bucket you created in the [Create Dependent Resources](#examples-python-s3-resources) section\.

1. Use your preferred compression application to compress the `streaming-file-sink.py` and `flink-sql-connector-kinesis_2.12-1.13.2.jar` files\. Name the archive `myapp.zip`\.

1. In the Amazon S3 console, choose the **ka\-app\-code\-*<username>*** bucket, and choose **Upload**\.

1. In the **Select files** step, choose **Add files**\. Navigate to the `myapp.zip` file that you created in the previous step\. 

1. You don't need to change any of the settings for the object, so choose **Upload**\.

Your application code is now stored in an Amazon S3 bucket where your application can access it\.

## Create and Run the Kinesis Data Analytics Application<a name="examples-python-s3-create-run"></a>

Follow these steps to create, configure, update, and run the application using the console\.

### Create the Application<a name="examples-python-s3-create"></a>

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

### Configure the Application<a name="examples-python-s3-configure"></a>

1. On the **MyApplication** page, choose **Configure**\.

1. On the **Configure application** page, provide the **Code location**:
   + For **Amazon S3 bucket**, enter **ka\-app\-code\-*<username>***\.
   + For **Path to Amazon S3 object**, enter **myapp\.zip**\.

1. Under **Access to application resources**, for **Access permissions**, choose **Create / update IAM role `kinesis-analytics-MyApplication-us-west-2`**\.

1. Under **Properties**, choose **Add group**\. For **Group ID**, enter **consumer\.config\.0**\.

1. Enter the following application properties and values:    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/kinesisanalytics/latest/java/examples-python-s3.html)

   Choose **Save**\.

1. Under **Properties**, choose **Add group** again\. For **Group ID**, enter **kinesis\.analytics\.flink\.run\.options**\. This special property group tells your application where to find its code resources\. For more information, see [Specifying your Code Files](how-python-creating.md#how-python-creating-code)\.

1. Enter the following application properties and values:    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/kinesisanalytics/latest/java/examples-python-s3.html)

1. Under **Properties**, choose **Add group** again\. For **Group ID**, enter **sink\.config\.0**\. This special property group tells your application where to find its code resources\. For more information, see [Specifying your Code Files](how-python-creating.md#how-python-creating-code)\.

1. Enter the following application properties and values: \(replace *bucket\-name* with the actual name of your Amazon S3 bucket\.\)    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/kinesisanalytics/latest/java/examples-python-s3.html)

1. Under **Monitoring**, ensure that the **Monitoring metrics level** is set to **Application**\.

1. For **CloudWatch logging**, select the **Enable** check box\.

1. Choose **Update**\.

**Note**  
When you choose to enable CloudWatch logging, Kinesis Data Analytics creates a log group and log stream for you\. The names of these resources are as follows:   
Log group: `/aws/kinesis-analytics/MyApplication`
Log stream: `kinesis-analytics-log-stream`
This log stream is used to monitor the application\. This is not the same log stream that the application uses to send results\.

### Edit the IAM Policy<a name="examples-python-s3-iam"></a>

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
                   "arn:aws:s3:::ka-app-code-<username>/myapp.zip"
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

### Run the Application<a name="examples-python-s3-run"></a>

1. On the **MyApplication** page, choose **Run**\. Leave the **Run without snapshot** option selected, and confirm the action\.

1. When the application is running, refresh the page\. The console shows the **Application graph**\.

You can check the Kinesis Data Analytics metrics on the CloudWatch console to verify that the application is working\. 

## Clean Up AWS Resources<a name="examples-python-s3-cleanup"></a>

This section includes procedures for cleaning up AWS resources created in the Sliding Window tutorial\.

**Topics**
+ [Delete Your Kinesis Data Analytics Application](#examples-python-s3-cleanup-app)
+ [Delete Your Kinesis Data Stream](#examples-python-s3-cleanup-stream)
+ [Delete Your Amazon S3 Objects and Bucket](#examples-python-s3-cleanup-s3)
+ [Delete Your IAM Resources](#examples-python-s3-cleanup-iam)
+ [Delete Your CloudWatch Resources](#examples-python-s3-cleanup-cw)

### Delete Your Kinesis Data Analytics Application<a name="examples-python-s3-cleanup-app"></a>

1. Open the Kinesis Data Analytics console at [ https://console\.aws\.amazon\.com/kinesisanalytics](https://console.aws.amazon.com/kinesisanalytics)\.

1. in the Kinesis Data Analytics panel, choose **MyApplication**\.

1. In the application's page, choose **Delete** and then confirm the deletion\.

### Delete Your Kinesis Data Stream<a name="examples-python-s3-cleanup-stream"></a>

1. Open the Kinesis console at [https://console\.aws\.amazon\.com/kinesis](https://console.aws.amazon.com/kinesis)\.

1. In the Kinesis Data Streams panel, choose **ExampleInputStream**\.

1. In the **ExampleInputStream** page, choose **Delete Kinesis Stream** and then confirm the deletion\.

### Delete Your Amazon S3 Objects and Bucket<a name="examples-python-s3-cleanup-s3"></a>

1. Open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. Choose the **ka\-app\-code\-*<username>* bucket\.**

1. Choose **Delete** and then enter the bucket name to confirm deletion\.

### Delete Your IAM Resources<a name="examples-python-s3-cleanup-iam"></a>

1. Open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. In the navigation bar, choose **Policies**\.

1. In the filter control, enter **kinesis**\.

1. Choose the **kinesis\-analytics\-service\-MyApplication\-*<your\-region>*** policy\.

1. Choose **Policy Actions** and then choose **Delete**\.

1. In the navigation bar, choose **Roles**\.

1. Choose the **kinesis\-analytics\-MyApplication\-*<your\-region>*** role\.

1. Choose **Delete role** and then confirm the deletion\.

### Delete Your CloudWatch Resources<a name="examples-python-s3-cleanup-cw"></a>

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. In the navigation bar, choose **Logs**\.

1. Choose the **/aws/kinesis\-analytics/MyApplication** log group\.

1. Choose **Delete Log Group** and then confirm the deletion\.