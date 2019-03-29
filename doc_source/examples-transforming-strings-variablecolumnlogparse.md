# Example: Split Strings into Multiple Fields \(VARIABLE\_COLUMN\_LOG\_PARSE Function\)<a name="examples-transforming-strings-variablecolumnlogparse"></a>

This example uses the `VARIABLE_COLUMN_LOG_PARSE` function to manipulate strings in Kinesis Data Analytics\. `VARIABLE_COLUMN_LOG_PARSE` splits an input string into fields separated by a delimiter character or a delimiter string\. For more information, see [VARIABLE\_COLUMN\_LOG\_PARSE](https://docs.aws.amazon.com/kinesisanalytics/latest/sqlref/sql-reference-variable-column-log-parse.html) in the *Amazon Kinesis Data Analytics SQL Reference*\.

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

You then create an Amazon Kinesis data analytics application on the console, using the Kinesis stream as the streaming source\. The discovery process reads sample records on the streaming source and infers an in\-application schema with four columns, as shown following:

![\[Console screenshot showing in-application schema with 4 columns.\]](http://docs.aws.amazon.com/kinesisanalytics/latest/dev/images/unstructured-10.png)

Then, you use the application code with the `VARIABLE_COLUMN_LOG_PARSE` function to parse the comma\-separated values, and insert normalized rows in another in\-application stream, as shown following:

![\[Console screenshot showing real-time analytics tab with in-application stream.\]](http://docs.aws.amazon.com/kinesisanalytics/latest/dev/images/unstructured-20.png)

**Topics**
+ [Step 1: Create a Kinesis Data Stream](#examples-transforming-strings-variablecolumnlogparse-1)
+ [Step 2: Create the Kinesis Data Analytics Application](#examples-transforming-strings-variablecolumnlogparse-2)

## Step 1: Create a Kinesis Data Stream<a name="examples-transforming-strings-variablecolumnlogparse-1"></a>

Create an Amazon Kinesis data stream and populate the log records as follows:

1. Sign in to the AWS Management Console and open the Kinesis console at [https://console\.aws\.amazon\.com/kinesis](https://console.aws.amazon.com/kinesis)\.

1. Choose **Data Streams** in the navigation pane\.

1. Choose **Create Kinesis stream**, and create a stream with one shard\. For more information, see [Create a Stream](https://docs.aws.amazon.com/streams/latest/dev/learning-kinesis-module-one-create-stream.html) in the *Amazon Kinesis Data Streams Developer Guide*\.

1. Run the following Python code to populate the sample log records\. This simple code continuously writes the same log record to the stream\.

   ```
    
   import json
   import boto3
   import random
   
   kinesis = boto3.client('kinesis')
   
   def getHighHeartRate():
       data = {}
       data['Col_A'] = 'a'
       data['Col_B'] = 'b'
       data['Col_C'] = 'c'
       data['Col_E_Unstructured'] = 'x,y,z'
       return data
   
   while True:
           data = json.dumps(getHighHeartRate())
           print(data)
           kinesis.put_record(
                   StreamName="ExampleInputStream",
                   Data=data,
                   PartitionKey="partitionkey")
   ```

## Step 2: Create the Kinesis Data Analytics Application<a name="examples-transforming-strings-variablecolumnlogparse-2"></a>

Create an Amazon Kinesis data analytics application as follows:

1. Open the Kinesis Data Analytics console at [ https://console\.aws\.amazon\.com/kinesisanalytics](https://console.aws.amazon.com/kinesisanalytics)\.

1. Choose **Create application**, type an application name, and choose **Create application**\.

1. On the application details page, choose **Connect streaming data**\. 

1. On the **Connect to source** page, do the following:

   1. Choose the stream that you created in the preceding section\.

   1. Choose the option to create an IAM role\.

   1. Choose **Discover schema**\. Wait for the console to show the inferred schema and samples records used to infer the schema for the in\-application stream created\. Note that the inferred schema has only one column\.

   1. Choose **Save and continue**\.

1. On the application details page, choose **Go to SQL editor**\. To start the application, choose **Yes, start application** in the dialog box that appears\.

1. In the SQL editor, write application code, and verify the results:

   1. Copy the following application code and paste it into the editor:

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

   1. Choose **Save and run SQL**\. On the **Real\-time analytics **tab, you can see all the in\-application streams that the application created and verify the data\.