# Example: Extracting a Portion of a String \(SUBSTRING Function\)<a name="examples-transforming-strings-substring"></a>

This example uses the `SUBSTRING` function to transform a string in Amazon Kinesis Data Analytics\. The `SUBSTRING` function extracts a portion of a source string starting at a specific position\. For more information, see [SUBSTRING](https://docs.aws.amazon.com/kinesisanalytics/latest/sqlref/sql-reference-substring.html) in the *Amazon Kinesis Data Analytics SQL Reference*\. 

In this example, you write the following records to an Amazon Kinesis data stream\. 

```
{ "REFERRER" : "http://www.amazon.com" }
{ "REFERRER" : "http://www.amazon.com"}
{ "REFERRER" : "http://www.amazon.com"}
...
```

You then create an Amazon Kinesis data analytics application on the console, using the Kinesis data stream as the streaming source\. The discovery process reads sample records on the streaming source and infers an in\-application schema with one column \(`REFERRER`\), as shown\.

![\[Console screenshot showing the in-application schema with a list of URLs in the referrer column.\]](http://docs.aws.amazon.com/kinesisanalytics/latest/dev/images/referrer-10.png)

Then, you use the application code with the `SUBSTRING` function to parse the URL string to retrieve the company name\. Then you insert the resulting data into another in\-application stream, as shown following: 

![\[Console screenshot showing real-time analytics tab with resulting data in the in-application stream.\]](http://docs.aws.amazon.com/kinesisanalytics/latest/dev/images/referrer-20.png)

**Topics**
+ [Step 1: Create a Kinesis Data Stream](#examples-transforming-strings-substring-1)
+ [Step 2: Create the Kinesis Data Analytics Application](#examples-transforming-strings-substring-2)

## Step 1: Create a Kinesis Data Stream<a name="examples-transforming-strings-substring-1"></a>

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
       data['REFERRER'] = 'http://www.amazon.com'
       return data
   
   while True:
           data = json.dumps(getReferrer())
           print(data)
           kinesis.put_record(
                   StreamName="ExampleInputStream",
                   Data=data,
                   PartitionKey="partitionkey")
   ```

## Step 2: Create the Kinesis Data Analytics Application<a name="examples-transforming-strings-substring-2"></a>

Next, create an Amazon Kinesis data analytics application as follows:

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
      -- CREATE OR REPLACE STREAM for cleaned up referrer
      CREATE OR REPLACE STREAM "DESTINATION_SQL_STREAM" (
          "ingest_time" TIMESTAMP,
          "referrer" VARCHAR(32));
          
      CREATE OR REPLACE PUMP "myPUMP" AS 
         INSERT INTO "DESTINATION_SQL_STREAM"
            SELECT STREAM 
               "APPROXIMATE_ARRIVAL_TIME", 
               SUBSTRING("referrer", 12, (POSITION('.com' IN "referrer") - POSITION('www.' IN "referrer") - 4)) 
            FROM "SOURCE_SQL_STREAM_001";
      ```

   1. Choose **Save and run SQL**\. On the **Real\-time analytics **tab, you can see all the in\-application streams that the application created and verify the data\. 