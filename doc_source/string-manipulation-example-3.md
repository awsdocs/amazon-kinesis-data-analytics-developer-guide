# Example: String Manipulation \(SUBSTRING Function\)<a name="string-manipulation-example-3"></a>

In this example, you write the following records to an Amazon Kinesis stream\. 

```
{ "referrer" : "http://www.stackoverflow.com" }
{ "referrer" : "http://www.amazon.com"}
{ "referrer" : "http://www.amazon.com"}
...
```

You then create an Amazon Kinesis Data Analytics application in the console, with the Kinesis stream as the streaming source\. The discovery process reads sample records on the streaming source and infers an in\-application schema with one column \(log\) as shown\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/kinesisanalytics/latest/dev/images/referrer-10.png)

Then, you use the application code with the `SUBSTRING` function to parse the URL string to retrieve the company name\. Then insert the resulting data into another in\-application stream, as shown following: 

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/kinesisanalytics/latest/dev/images/referrer-20.png)

## Step 1: Create a Kinesis Data Stream<a name="w3ab1c22c10b9c10c20"></a>

Create an Amazon Kinesis data stream and populate log records as follows:

1. Sign in to the AWS Management Console and open the Kinesis Data Analytics console at [ https://console\.aws\.amazon\.com/kinesisanalytics](https://console.aws.amazon.com/kinesisanalytics)\.

1. Choose **Kinesis Stream**, and then create a stream with one shard\.

1. Run the following Python code to populate sample log records\. The Python code is simple; it continuously writes same log record to the stream\.

   ```
   import json
   from boto import kinesis
   import random
   
   kinesis = kinesis.connect_to_region("us-east-1")
   def getReferrer():
       data = {}
       data['referrer'] = 'http://www.amazon.com'
       return data
   
   while True:
           data = json.dumps(getReferrer())
           print data
           kinesis.put_record("teststreamforkinesisanalyticsapps", data, "partitionkey")
   ```

## Step 2: Create the Amazon Kinesis Data Analytics Application<a name="w3ab1c22c10b9c10c22"></a>

Create an Amazon Kinesis Data Analytics application as follows:

1. Sign in to the AWS Management Console and open the Kinesis Data Analytics console at [ https://console\.aws\.amazon\.com/kinesisanalytics](https://console.aws.amazon.com/kinesisanalytics)\.

1. Choose **Create new application**, and specify an application name\.

1. On the application hub, connect to the source\. 

1. On the **Source** page, do the following:
   + Select the stream you created in the preceding section\. 
   + Choose the create IAM role option\.
   + Wait for the console to show the inferred schema and samples records used to infer the schema for the in\-application stream created\. Note that the inferred schema has only one column\.
   + Choose **Save and continue**\.

1. On the application hub, choose **Go to SQL editor**\. To start the application, choose **yes** in the dialog box that appears\.

1. In the SQL editor, write the application code and verify the results as follows:
   + Copy the following application code and paste it into the editor\.

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
   + Choose **Save and run SQL**\. On the **Real\-time analytics **tab, you can see all of the in\-application streams that the application created and verify the data\. 