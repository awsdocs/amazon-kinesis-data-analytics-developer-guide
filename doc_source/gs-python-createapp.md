# Create and Run a Kinesis Data Analytics for Python Application<a name="gs-python-createapp"></a>

In this exercise, you create a Kinesis Data Analytics application for Python application with an Kinesis stream as a source and a sink\.

**Topics**
+ [Create Dependent Resources](#gs-python-resources)
+ [Write Sample Records to the Input Stream](#gs-python-2)
+ [Create and Examine the Apache Flink Streaming Python Code](#gs-python-5)
+ [Upload the Apache Flink Streaming Python Code](#gs-python-6)
+ [Create and Run the Kinesis Data Analytics Application](#gs-python-7)
+ [Next Step](#gs-python-next-step-4)

## Create Dependent Resources<a name="gs-python-resources"></a>

Before you create a Kinesis Data Analytics for Apache Flink application for this exercise, you create the following dependent resources: 
+ Two Kinesis streams for input and output\.
+ An Amazon S3 bucket to store the application's code and output \(`ka-app-<username>`\) 

### Create Two Kinesis Streams<a name="gs-python-resources-streams"></a>

Before you create a Kinesis Data Analytics application for this exercise, create two Kinesis data streams \(`ExampleInputStream` and `ExampleOutputStream`\)\. Your application uses these streams for the application source and destination streams\.

You can create these streams using either the Amazon Kinesis console or the following AWS CLI command\. For console instructions, see [Creating and Updating Data Streams](https://docs.aws.amazon.com/kinesis/latest/dev/amazon-kinesis-streams.html) in the *Amazon Kinesis Data Streams Developer Guide*\. 

**To create the data streams \(AWS CLI\)**

1. To create the first stream \(`ExampleInputStream`\), use the following Amazon Kinesis `create-stream` AWS CLI command\.

   ```
   $ aws kinesis create-stream \
   --stream-name ExampleInputStream \
   --shard-count 1 \
   --region us-west-2 \
   --profile adminuser
   ```

1. To create the second stream that the application uses to write output, run the same command, changing the stream name to `ExampleOutputStream`\.

   ```
   $ aws kinesis create-stream \
   --stream-name ExampleOutputStream \
   --shard-count 1 \
   --region us-west-2 \
   --profile adminuser
   ```

### Create an Amazon S3 Bucket<a name="gs-python-resources-s3"></a>

You can create the Amazon S3 bucket using the console\. For instructions for creating this resource, see the following topics:
+ [How Do I Create an S3 Bucket?](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/create-bucket.html) in the *Amazon Simple Storage Service User Guide*\. Give the Amazon S3 bucket a globally unique name by appending your login name, such as **ka\-app\-*<username>***\. 

### Other Resources<a name="gs-python-resources-cw"></a>

When you create your application, Kinesis Data Analytics creates the following Amazon CloudWatch resources if they don't already exist:
+ A log group called `/aws/kinesis-analytics-java/MyApplication`\.
+ A log stream called `kinesis-analytics-log-stream`\.

## Write Sample Records to the Input Stream<a name="gs-python-2"></a>

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
   import time
   
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
           time.sleep(2)
   
   
   if __name__ == '__main__':
       generate(STREAM_NAME, boto3.client('kinesis'))
   ```

1. Run the `stock.py` script: 

   ```
   $ python stock.py
   ```

   Keep the script running while completing the rest of the tutorial\.

## Create and Examine the Apache Flink Streaming Python Code<a name="gs-python-5"></a>

The Python application code for this example is available from GitHub\. To download the application code, do the following:

1. Install the Git client if you haven't already\. For more information, see [Installing Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)\. 

1. Clone the remote repository with the following command:

   ```
   git clone https://github.com/aws-samples/amazon-kinesis-data-analytics-java-examples
   ```

1. Navigate to the `amazon-kinesis-data-analytics-java-examples/python/GettingStarted` directory\.

The application code is located in the `getting-started.py` file\. Note the following about the application code:
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
+ The application creates two tables, then writes the contents of one table to the other\.

  ```
      # 2. Creates a source table from a Kinesis Data Stream
      table_env.execute_sql(
          create_table(input_table_name, input_stream, input_region, stream_initpos)
      )
  
      # 3. Creates a sink table writing to a Kinesis Data Stream
      table_env.execute_sql(
          create_table(output_table_name, output_stream, output_region, stream_initpos)
      )
  
      # 4. Inserts the source table data into the sink table
      table_result = table_env.execute_sql("INSERT INTO {0} SELECT * FROM {1}"
                             .format(output_table_name, input_table_name))
  ```
+ The application uses the Flink connector, from the [flink\- sql\-connector\-kinesis\_2\.12/1\.13\.2](https://mvnrepository.com/artifact/org.apache.flink/flink-                         sql-connector-kinesis_2.12/1.13.2) file\.



## Upload the Apache Flink Streaming Python Code<a name="gs-python-6"></a>

In this section, you create an Amazon S3 bucket and upload your application code\.

**To upload the application code using the console:**

1. Use your preferred compression application to compress the `streaming-file-sink.py` and [https://mvnrepository\.com/artifact/org\.apache\.flink/flink\- sql\-connector\-kinesis\_2\.12/1\.13\.2](https://mvnrepository.com/artifact/org.apache.flink/flink-sql-connector-kinesis_2.12/1.13.2) files\. Name the archive `myapp.zip`\. If you include the outer folder in your archive, you must include this in the path with the code in your configuration file\(s\): `GettingStarted/getting-started.py`\.

1. Open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. Choose **Create bucket**\.

1. Enter **ka\-app\-code\-*<username>*** in the **Bucket name** field\. Add a suffix to the bucket name, such as your user name, to make it globally unique\. Choose **Next**\.

1. In the **Configure options** step, keep the settings as they are, and choose **Next**\.

1. In the **Set permissions** step, keep the settings as they are, and choose **Next**\.

1. Choose **Create bucket**\.

1. In the Amazon S3 console, choose the **ka\-app\-code\-*<username>*** bucket, and choose **Upload**\.

1. In the **Select files** step, choose **Add files**\. Navigate to the `myapp.zip` file that you created in the previous step\. Choose **Next**\.

1. You don't need to change any of the settings for the object, so choose **Upload**\.

**To upload the application code using the AWS CLI:**

1. Use your preferred compression application to compress the `streaming-file-sink.py` and [https://mvnrepository\.com/artifact/org\.apache\.flink/flink\- sql\-connector\-kinesis\_2\.12/1\.13\.2](https://mvnrepository.com/artifact/org.apache.flink/flink-sql-connector-kinesis_2.12/1.13.2) files\. Name the archive `myapp.zip`\. If you include the outer folder in your archive, you must include this in the path with the code in your configuration file\(s\): `GettingStarted/getting-started.py`\.

1. Run the following command:

    `$ aws s3 --region aws region cp myapp.zip s3://ka-app-code-<username>`

Your application code is now stored in an Amazon S3 bucket where your application can access it\.

## Create and Run the Kinesis Data Analytics Application<a name="gs-python-7"></a>

Follow these steps to create, configure, update, and run the application using the console\.

### Create the Application<a name="gs-python-7-console-create"></a>

1. Open the Kinesis Data Analytics console at [ https://console\.aws\.amazon\.com/kinesisanalytics](https://console.aws.amazon.com/kinesisanalytics)\.

1. On the Kinesis Data Analytics dashboard, choose **Create analytics application**\.

1. On the **Kinesis Analytics \- Create application** page, provide the application details as follows:
   + For **Application name**, enter **MyApplication**\.
   + For **Description**, enter **My java test app**\.
   + For **Runtime**, choose **Apache Flink**\.
   + Leave the version as **Apache Flink version 1\.13\.2 \(Recommended version\)**\.

1. For **Access permissions**, choose **Create / update IAM role `kinesis-analytics-MyApplication-us-west-2`**\.

1. Choose **Create application**\.

**Note**  
When you create a Kinesis Data Analytics application using the console, you have the option of having an IAM role and policy created for your application\. Your application uses this role and policy to access its dependent resources\. These IAM resources are named using your application name and Region as follows:  
Policy: `kinesis-analytics-service-MyApplication-us-west-2`
Role: `kinesis-analytics-MyApplication-us-west-2`

### Configure the Application<a name="gs-python-7-console-configure"></a>

Use the following procedure to configure the application\.

**To configure the application**

1. On the **MyApplication** page, choose **Configure**\.

1. On the **Configure application** page, provide the **Code location**:
   + For **Amazon S3 bucket**, enter **ka\-app\-code\-*<username>***\.
   + For **Path to Amazon S3 object**, enter **myapp\.zip**\.

1. Under **Access to application resources**, for **Access permissions**, choose **Create / update IAM role `kinesis-analytics-MyApplication-us-west-2`**\.

1. Under **Properties**, choose **Add group**\. For **Group ID**, enter **consumer\.config\.0**\.

1. Enter the following application properties and values:    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/kinesisanalytics/latest/java/gs-python-createapp.html)

   Choose **Save**\.

1. Under **Properties**, choose **Add group** again\. For **Group ID**, enter **producer\.config\.0**\. 

1. Enter the following application properties and values:    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/kinesisanalytics/latest/java/gs-python-createapp.html)

1. Under **Properties**, choose **Add group** again\. For **Group ID**, enter **kinesis\.analytics\.flink\.run\.options**\. This special property group tells your application where to find its code resources\. For more information, see [Specifying your Code Files](how-python-creating.md#how-python-creating-code)\.

1. Enter the following application properties and values:    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/kinesisanalytics/latest/java/gs-python-createapp.html)

1. Under **Monitoring**, ensure that the **Monitoring metrics level** is set to **Application**\.

1. For **CloudWatch logging**, choose the **Enable** check box\.

1. Choose **Update**\.

**Note**  
When you choose to enable Amazon CloudWatch logging, Kinesis Data Analytics creates a log group and log stream for you\. The names of these resources are as follows:   
Log group: `/aws/kinesis-analytics/MyApplication`
Log stream: `kinesis-analytics-log-stream`

### Edit the IAM Policy<a name="gs-python-7-console-iam"></a>

Edit the IAM policy to add permissions to access the Amazon S3 bucket\.

**To edit the IAM policy to add S3 bucket permissions**

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
                   "s3:GetObjectVersion"
               ],
               "Resource": [
                   "arn:aws:s3:::ka-app-code-username/myapp.zip"
               ]
           },
           {
               "Sid": "DescribeLogGroups",
               "Effect": "Allow",
               "Action": [
                   "logs:DescribeLogGroups"
               ],
               "Resource": [
                   "arn:aws:logs:us-west-2:012345678901:log-group:*"
               ]
           },
           {
               "Sid": "DescribeLogStreams",
               "Effect": "Allow",
               "Action": [
                   "logs:DescribeLogStreams"
               ],
               "Resource": [
                   "arn:aws:logs:us-west-2:012345678901:log-group:/aws/kinesis-analytics/MyApplication:log-stream:*"
               ]
           },
           {
               "Sid": "PutLogEvents",
               "Effect": "Allow",
               "Action": [
                   "logs:PutLogEvents"
               ],
               "Resource": [
                   "arn:aws:logs:us-west-2:012345678901:log-group:/aws/kinesis-analytics/MyApplication:log-stream:kinesis-analytics-log-stream"
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

### Run the Application<a name="gs-python-7-console-run"></a>

Use the following procedure to run the application\.

**To run the application**

1. On the **MyApplication** page, choose **Run**\. Confirm the action\.

1. When the application is running, refresh the page\. The console shows the **Application graph**\.

### Stop the Application<a name="gs-python-7-console-stop"></a>

To stop the application, on the **MyApplication** page, choose **Stop**\. Confirm the action\.

## Next Step<a name="gs-python-next-step-4"></a>

[Clean Up AWS Resources](gs-python-cleanup.md)