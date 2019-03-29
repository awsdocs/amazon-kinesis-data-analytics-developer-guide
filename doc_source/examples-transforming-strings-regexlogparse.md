# Example: Parsing Log Strings Based on Regular Expressions \(REGEX\_LOG\_PARSE Function\)<a name="examples-transforming-strings-regexlogparse"></a>

This example uses the `REGEX_LOG_PARSE` function to transform a string in Amazon Kinesis Data Analytics\. `REGEX_LOG_PARSE` parses a string based on default Java regular expression patterns\. For more information, see [REGEX\_LOG\_PARSE](https://docs.aws.amazon.com/kinesisanalytics/latest/sqlref/sql-reference-regex-log-parse.html) in the *Amazon Kinesis Data Analytics SQL Reference*\.

In this example, you write the following records to an Amazon Kinesis stream: 

```
{"LOGENTRY": "203.0.113.24 - - [25/Mar/2018:15:25:37 -0700] \"GET /index.php HTTP/1.1\" 200 125 \"-\" \"Mozilla/5.0 [en] Gecko/20100101 Firefox/52.0\""}
{"LOGENTRY": "203.0.113.24 - - [25/Mar/2018:15:25:37 -0700] \"GET /index.php HTTP/1.1\" 200 125 \"-\" \"Mozilla/5.0 [en] Gecko/20100101 Firefox/52.0\""}
{"LOGENTRY": "203.0.113.24 - - [25/Mar/2018:15:25:37 -0700] \"GET /index.php HTTP/1.1\" 200 125 \"-\" \"Mozilla/5.0 [en] Gecko/20100101 Firefox/52.0\""}
...
```

You then create an Amazon Kinesis data analytics application on the console, with the Kinesis data stream as the streaming source\. The discovery process reads sample records on the streaming source and infers an in\-application schema with one column \(LOGENTRY\), as shown following\.

![\[Console screenshot showing in-application schema with LOGENTRY column.\]](http://docs.aws.amazon.com/kinesisanalytics/latest/dev/images/ex_regex_log_parse_0.png)

Then, you use the application code with the `REGEX_LOG_PARSE` function to parse the log string to retrieve the data elements\. You insert the resulting data into another in\-application stream, as shown in the following screenshot: 

![\[Console screenshot showing the resulting data table with ROWTIME, LOGENTRY, MATCH1, and MATCH2 columns.\]](http://docs.aws.amazon.com/kinesisanalytics/latest/dev/images/ex_regex_log_parse_1.png)

**Topics**
+ [Step 1: Create a Kinesis Data Stream](#examples-transforming-strings-regexlogparse-1)
+ [Step 2: Create the Kinesis Data Analytics Application](#examples-transforming-strings-regexlogparse-2)

## Step 1: Create a Kinesis Data Stream<a name="examples-transforming-strings-regexlogparse-1"></a>

Create an Amazon Kinesis data stream and populate the log records as follows:

1. Sign in to the AWS Management Console and open the Kinesis console at [https://console\.aws\.amazon\.com/kinesis](https://console.aws.amazon.com/kinesis)\.

1. Choose **Data Streams** in the navigation pane\.

1. Choose **Create Kinesis stream**, and create a stream with one shard\. For more information, see [Create a Stream](https://docs.aws.amazon.com/streams/latest/dev/learning-kinesis-module-one-create-stream.html) in the *Amazon Kinesis Data Streams Developer Guide*\.

1. Run the following Python code to populate sample log records\. This simple code continuously writes the same log record to the stream\.

   ```
    
   import json
   import boto3
   import random
   
   kinesis = boto3.client('kinesis')
   def getReferrer():
       data = {}
       data['LOGENTRY'] = '203.0.113.24 - - [25/Mar/2018:15:25:37 -0700] "GET /index.php HTTP/1.1" 200 125 "-" "Mozilla/5.0 [en] Gecko/20100101 Firefox/52.0"'
       return data
   
   while True:
           data = json.dumps(getReferrer())
           print(data)
           kinesis.put_record(
                   StreamName="ExampleInputStream",
                   Data=data,
                   PartitionKey="partitionkey")
   ```

## Step 2: Create the Kinesis Data Analytics Application<a name="examples-transforming-strings-regexlogparse-2"></a>

Next, create an Amazon Kinesis data analytics application as follows:

1. Open the Kinesis Data Analytics console at [ https://console\.aws\.amazon\.com/kinesisanalytics](https://console.aws.amazon.com/kinesisanalytics)\.

1. Choose **Create application**, and specify an application name\.

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
      CREATE OR REPLACE STREAM "DESTINATION_SQL_STREAM" (logentry VARCHAR(24), match1 VARCHAR(24), match2 VARCHAR(24));
      
      CREATE OR REPLACE PUMP "STREAM_PUMP" AS INSERT INTO "DESTINATION_SQL_STREAM"
          SELECT STREAM T.LOGENTRY, T.REC.COLUMN1, T.REC.COLUMN2
          FROM 
               (SELECT STREAM LOGENTRY,
                   REGEX_LOG_PARSE(LOGENTRY, '(\w.+) (\d.+) (\w.+) (\w.+)') AS REC
                   FROM SOURCE_SQL_STREAM_001) AS T;
      ```

   1. Choose **Save and run SQL**\. On the **Real\-time analytics **tab, you can see all the in\-application streams that the application created and verify the data\.