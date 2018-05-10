# Example: String Manipulation \(W3C\_LOG\_PARSE Function\)<a name="string-manipulation-example-1"></a>

In this example, you write log records to an Amazon Kinesis data stream\. Example logs are shown following:

```
{"Log":"192.168.254.30 - John [24/May/2004:22:01:02 -0700] "GET /icons/apache_pba.gif HTTP/1.1" 304 0"}
{"Log":"192.168.254.30 - John [24/May/2004:22:01:03 -0700] "GET /icons/apache_pbb.gif HTTP/1.1" 304 0"}
{"Log":"192.168.254.30 - John [24/May/2004:22:01:04 -0700] "GET /icons/apache_pbc.gif HTTP/1.1" 304 0"}
...
```

You then create an Amazon Kinesis data analytics application in the console, with the Kinesis data stream as the streaming source\. The discovery process reads sample records on the streaming source and infers an in\-application schema with one column \(log\), as shown following:

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/kinesisanalytics/latest/dev/images/log-10.png)

Then, you use the application code with the `W3C_LOG_PARSE` function to parse the log, and create another in\-application stream with various log fields in separate columns, as shown following:

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/kinesisanalytics/latest/dev/images/log-20.png)

## Step 1: Create a Kinesis Data Stream<a name="w3ab1c22c10b9b4c18"></a>

Create an Amazon Kinesis data stream and populate log records as follows:

1. Sign in to the AWS Management Console and open the Kinesis Data Analytics console at [ https://console\.aws\.amazon\.com/kinesisanalytics](https://console.aws.amazon.com/kinesisanalytics)\.

1. Choose **Data Streams** and then create a stream with one shard\.

1. Run the following Python code to populate sample log records\. The Python code is simple; it continuously writes same log record to the stream\.

   ```
   import json
   from boto import kinesis
   import random
   
   kinesis = kinesis.connect_to_region("us-east-1")
   def getHighHeartRate():
       data = {}
       data['log'] = '192.168.254.30 - John [24/May/2004:22:01:02 -0700] "GET /icons/apache_pb.gif HTTP/1.1" 304 0'
       return data
   
   while True:
           data = json.dumps(getHighHeartRate())
           print data
           kinesis.put_record("stream-name", data, "partitionkey")
   ```

## Step 2: Create the Amazon Kinesis Data Analytics Application<a name="w3ab1c22c10b9b4c20"></a>

Create an Amazon Kinesis Data Analytics application as follows:

1. Sign in to the AWS Management Console and open the Kinesis Data Analytics console at [ https://console\.aws\.amazon\.com/kinesisanalytics](https://console.aws.amazon.com/kinesisanalytics)\.

1. Choose **Create new application**, and specify an application name\.

1. On the application hub, connect to the source\. 

1. On the **Source** page, do the following:
   + Select the stream that you created in the preceding section\. 
   + Choose the create IAM role option\.
   + Wait for the console to show the inferred schema and samples records used to infer the schema for the in\-application stream created\. Note that the inferred schema has only one column\.
   + Choose **Save and continue**\.

1. On the application hub, choose **Go to SQL editor**\. To start the application, choose **yes** in the dialog box that appears\.

1. In the SQL editor, write the application code and verify the results as follows:
   + Copy the following application code and paste it into the editor\.

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
   + Choose **Save and run SQL**\. On the **Real\-time analytics **tab, you can see all of the in\-application streams that the application created and verify the data\. 