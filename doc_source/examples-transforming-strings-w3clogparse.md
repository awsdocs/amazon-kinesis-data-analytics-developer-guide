# Example: Parsing Web Logs \(W3C\_LOG\_PARSE Function\)<a name="examples-transforming-strings-w3clogparse"></a>

This example uses the `W3C_LOG_PARSE` function to transform a string in Amazon Kinesis Data Analytics\. You can use `W3C_LOG_PARSE` to format Apache logs quickly\. For more information, see [W3C\_LOG\_PARSE](https://docs.aws.amazon.com/kinesisanalytics/latest/sqlref/sql-reference-w3c-log-parse.html) in the *Amazon Kinesis Data Analytics SQL Reference*\.

In this example, you write log records to an Amazon Kinesis data stream\. Example logs are shown following:

```
{"Log":"192.168.254.30 - John [24/May/2004:22:01:02 -0700] "GET /icons/apache_pba.gif HTTP/1.1" 304 0"}
{"Log":"192.168.254.30 - John [24/May/2004:22:01:03 -0700] "GET /icons/apache_pbb.gif HTTP/1.1" 304 0"}
{"Log":"192.168.254.30 - John [24/May/2004:22:01:04 -0700] "GET /icons/apache_pbc.gif HTTP/1.1" 304 0"}
...
```

You then create an Amazon Kinesis data analytics application on the console, with the Kinesis data stream as the streaming source\. The discovery process reads sample records on the streaming source and infers an in\-application schema with one column \(log\), as shown following:

![\[Console screenshot showing formatted stream sample tab with the in-application schema containing the log column.\]](http://docs.aws.amazon.com/kinesisanalytics/latest/dev/images/log-10.png)

Then, you use the application code with the `W3C_LOG_PARSE` function to parse the log, and create another in\-application stream with various log fields in separate columns, as shown following:

![\[Console screenshot showing real-time analytics tab with in-application stream.\]](http://docs.aws.amazon.com/kinesisanalytics/latest/dev/images/log-20.png)

**Topics**
+ [Step 1: Create a Kinesis Data Stream](#examples-transforming-strings-w3clogparse-1)
+ [Step 2: Create the Kinesis Data Analytics Application](#examples-transforming-strings-w3clogparse-2)

## Step 1: Create a Kinesis Data Stream<a name="examples-transforming-strings-w3clogparse-1"></a>

Create an Amazon Kinesis data stream, and populate the log records as follows:

1. Sign in to the AWS Management Console and open the Kinesis console at [https://console\.aws\.amazon\.com/kinesis](https://console.aws.amazon.com/kinesis)\.

1. Choose **Data Streams** in the navigation pane\.

1. Choose **Create Kinesis stream**, and create a stream with one shard\. For more information, see [Create a Stream](https://docs.aws.amazon.com/streams/latest/dev/learning-kinesis-module-one-create-stream.html) in the *Amazon Kinesis Data Streams Developer Guide*\.

1. Run the following Python code to populate the sample log records\. This simple code continuously writes the same log record to the stream\.

   ```
    
   import json
   import boto3
   import random
   
   kinesis = boto3.client('kinesis')
   def getLog():
       data = {}
       data['log'] = '192.168.254.30 - John [24/May/2004:22:01:02 -0700] "GET /icons/apache_pb.gif HTTP/1.1" 304 0'
       return data
   
   while True:
           data = json.dumps(getLog())
           print(data)
           kinesis.put_record(
                   StreamName="ExampleInputStream",
                   Data=data,
                   PartitionKey="partitionkey")
   ```

## Step 2: Create the Kinesis Data Analytics Application<a name="examples-transforming-strings-w3clogparse-2"></a>

Create an Amazon Kinesis data analytics application as follows:

1. Open the Kinesis Data Analytics console at [ https://console\.aws\.amazon\.com/kinesisanalytics](https://console.aws.amazon.com/kinesisanalytics)\.

1. Choose **Create application**, type an application name, and choose **Create application**\.

1. On the application details page, choose **Connect streaming data**\.

1. On the **Connect to source** page, do the following:

   1. Choose the stream that you created in the preceding section\. 

   1. Choose the option to create an IAM role\.

   1. Choose **Discover schema**\. Wait for the console to show the inferred schema and samples records used to infer the schema for the in\-application stream created\. The inferred schema has only one column\.

   1. Choose **Save and continue**\.

1. On the application details page, choose **Go to SQL editor**\. To start the application, choose **Yes, start application** in the dialog box that appears\.

1. In the SQL editor, write the application code, and verify the results as follows:

   1. Copy the following application code and paste it into the editor\.

      ```
      CREATE OR REPLACE STREAM "DESTINATION_SQL_STREAM" (
      column1 VARCHAR(16),
      column2 VARCHAR(16),
      column3 VARCHAR(16),
      column4 VARCHAR(16),
      column5 VARCHAR(16),
      column6 VARCHAR(16),
      column7 VARCHAR(16));
      
      CREATE OR REPLACE PUMP "myPUMP" AS 
      INSERT INTO "DESTINATION_SQL_STREAM"
              SELECT STREAM
                  l.r.COLUMN1,
                  l.r.COLUMN2,
                  l.r.COLUMN3,
                  l.r.COLUMN4,
                  l.r.COLUMN5,
                  l.r.COLUMN6,
                  l.r.COLUMN7
              FROM (SELECT STREAM W3C_LOG_PARSE("log", 'COMMON')
                    FROM "SOURCE_SQL_STREAM_001") AS l(r);
      ```

   1. Choose **Save and run SQL**\. On the **Real\-time analytics **tab, you can see all the in\-application streams that the application created and verify the data\.