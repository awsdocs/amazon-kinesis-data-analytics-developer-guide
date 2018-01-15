# Example: String Manipulation \(VARIABLE\_COLUMN\_LOG\_PARSE Function\)<a name="string-manipulation-example-2"></a>

In this example, you write semi\-structured records to an Amazon Kinesis data stream\. The example records are as follows:

```
{ "Col_A" : "string",
  "Col_B" : "string",
  "Col_C" : "string",
  "Col_D_Unstructured" : "value,value,value,value"}
{ "Col_A" : "string",
  "Col_B" : "string",
  "Col_C" : "string",
  "Col_D_Unstructured" : "value,value,value,value"}
```

You then create an Amazon Kinesis Data Analytics application in the console, with the Kinesis stream as the streaming source\. The discovery process reads sample records on the streaming source and infer an in\-application schema with one column \(log\), as shown following:

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/kinesisanalytics/latest/dev/images/unstructured-10.png)

Then, you use the application code with the `VARIABLE_COLUMN_LOG_PARSE` function to parse the comma\-separated values, and insert normalized rows in another in\-application stream, as shown following:

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/kinesisanalytics/latest/dev/images/unstructured-20.png)

## Step 1: Create a Kinesis Data Stream<a name="w3ab1c24c10b7b8c18"></a>

Create an Amazon Kinesis data stream and populate log records as follows:

1. Sign in to the AWS Management Console and open the Kinesis Data Analytics console at [ https://console\.aws\.amazon\.com/kinesisanalytics](https://console.aws.amazon.com/kinesisanalytics)\.

1. Choose **Kinesis Stream** and then create a stream with one shard\.

1. Run the following Python code to populate sample log records\. The Python code is simple; it continuously writes same log record to the stream\.

   ```
   import json
   from boto import kinesis
   import random
   
   kinesis = kinesis.connect_to_region("us-east-1")
   def getHighHeartRate():
       data = {}
       data['Col_A'] = 'a'
       data['Col_B'] = 'b'
       data['Col_C'] = 'c'
       data['Col_E_Unstructured'] = 'x,y,z'
       return data
   
   while True:
           data = json.dumps(getHighHeartRate())
           print data
           kinesis.put_record("teststreamforkinesisanalyticsapps", data, "partitionkey")
   ```

## Step 2: Create the Amazon Kinesis Data Analytics Application<a name="w3ab1c24c10b7b8c20"></a>

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

1. In the SQL editor, write application code and verify results:

   + Copy the following application code and paste it into the editor\.

     ```
     CREATE OR REPLACE STREAM "DESTINATION_SQL_STREAM"(
                 "column_A" VARCHAR(16),
                 "column_B" VARCHAR(16),
                 "column_C" VARCHAR(16),
                 "COL_1" VARCHAR(16),             
                 "COL_2" VARCHAR(16),            
                 "COL_3" VARCHAR(16));
     
     CREATE OR REPLACE PUMP "SECOND_STREAM_PUMP" AS
     INSERT INTO "DESTINATION_SQL_STREAM"
        SELECT STREAM  t."Col_A", t."Col_B", t."Col_C",
                       t.r."COL_1", t.r."COL_2", t.r."COL_3"
        FROM (SELECT STREAM 
                "Col_A", "Col_B", "Col_C",
                VARIABLE_COLUMN_LOG_PARSE ("Col_E_Unstructured",
                                          'COL_1 TYPE VARCHAR(16), COL_2 TYPE VARCHAR(16), COL_3 TYPE VARCHAR(16)',
                                          ',') AS r 
              FROM "SOURCE_SQL_STREAM_001") as t;
     ```

   + Choose **Save and run SQL**\. On the **Real\-time analytics **tab, you can see all of the in\-application streams that the application created and verify the data\. 