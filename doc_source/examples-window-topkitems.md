# Example: Retrieving the Most Frequently Occurring Values \(TOP\_K\_ITEMS\_TUMBLING\)<a name="examples-window-topkitems"></a>

This Amazon Kinesis Data Analytics example demonstrates how to use the `TOP_K_ITEMS_TUMBLING` function to retrieve the most frequently occurring values in a tumbling window\. For more information, see [`TOP_K_ITEMS_TUMBLING` function](https://docs.aws.amazon.com/kinesisanalytics/latest/sqlref/top-k.html) in the *Amazon Kinesis Data Analytics SQL Reference*\. 

The `TOP_K_ITEMS_TUMBLING` function is useful when aggregating over tens or hundreds of thousands of keys, and you want to reduce your resource usage\. The function produces the same result as aggregating with `GROUP BY` and `ORDER BY` clauses\.

In this example, you write the following records to an Amazon Kinesis data stream: 

```
{"TICKER": "TBV"}
{"TICKER": "INTC"}
{"TICKER": "MSFT"}
{"TICKER": "AMZN"}
...
```

You then create a Kinesis Data Analytics application in the AWS Management Console, with the Kinesis data stream as the streaming source\. The discovery process reads sample records on the streaming source and infers an in\-application schema with one column \(`TICKER`\) as shown following\.

![\[Console screenshot showing the in-application schema with a ticker column.\]](http://docs.aws.amazon.com/kinesisanalytics/latest/dev/images/ex_topk_schema.png)

You use the application code with the `TOP_K_VALUES_TUMBLING` function to create a windowed aggregation of the data\. Then you insert the resulting data into another in\-application stream, as shown in the following screenshot: 

![\[Console screenshot showing the resulting data in an in-application stream.\]](http://docs.aws.amazon.com/kinesisanalytics/latest/dev/images/ex_topk.png)

In the following procedure, you create a Kinesis Data Analytics application that retrieves the most frequently occurring values in the input stream\.

**Topics**
+ [Step 1: Create a Kinesis Data Stream](#examples-window-topkitems-1)
+ [Step 2: Create the Kinesis Data Analytics Application](#examples-window-topkitems-2)

## Step 1: Create a Kinesis Data Stream<a name="examples-window-topkitems-1"></a>

Create an Amazon Kinesis data stream and populate the records as follows:

1. Sign in to the AWS Management Console and open the Kinesis console at [https://console\.aws\.amazon\.com/kinesis](https://console.aws.amazon.com/kinesis)\.

1. Choose **Data Streams** in the navigation pane\.

1. Choose **Create Kinesis stream**, and then create a stream with one shard\. For more information, see [Create a Stream](https://docs.aws.amazon.com/streams/latest/dev/learning-kinesis-module-one-create-stream.html) in the *Amazon Kinesis Data Streams Developer Guide*\.

1. To write records to a Kinesis data stream in a production environment, we recommend using either the [Kinesis Client Library](https://docs.aws.amazon.com/streams/latest/dev/developing-producers-with-kpl.html) or [Kinesis Data Streams API](https://docs.aws.amazon.com/streams/latest/dev/developing-producers-with-sdk.html)\. For simplicity, this example uses the following Python script to generate records\. Run the code to populate the sample ticker records\. This simple code continuously writes a random ticker record to the stream\. Leave the script running so that you can generate the application schema in a later step\.

   ```
    
   import json
   import boto3
   import random
   import datetime
   
   kinesis = boto3.client('kinesis')
   def getReferrer():
       data = {}
       now = datetime.datetime.now()
       str_now = now.isoformat()
       data['EVENT_TIME'] = str_now
       data['TICKER'] = random.choice(['AAPL', 'AMZN', 'MSFT', 'INTC', 'TBV'])
       price = random.random() * 100
       data['PRICE'] = round(price, 2)
       return data
   
   while True:
           data = json.dumps(getReferrer())
           print(data)
           kinesis.put_record(
                   StreamName="ExampleInputStream",
                   Data=data,
                   PartitionKey="partitionkey")
   ```

## Step 2: Create the Kinesis Data Analytics Application<a name="examples-window-topkitems-2"></a>

Create a Kinesis Data Analytics application as follows:

1. Open the Kinesis Data Analytics console at [ https://console\.aws\.amazon\.com/kinesisanalytics](https://console.aws.amazon.com/kinesisanalytics)\.

1. Choose **Create application**, type an application name, and choose **Create application**\.

1. On the application details page, choose **Connect streaming data** to connect to the source\. 

1. On the **Connect to source** page, do the following:

   1. Choose the stream that you created in the preceding section\. 

   1. Choose **Discover Schema**\. Wait for the console to show the inferred schema and samples records that are used to infer the schema for the in\-application stream created\. The inferred schema has one column\.

   1. Choose **Save schema and update stream samples**\. After the console saves the schema, choose **Exit**\.

   1. Choose **Save and continue**\.

1. On the application details page, choose **Go to SQL editor**\. To start the application, choose **Yes, start application** in the dialog box that appears\.

1. In the SQL editor, write the application code, and verify the results as follows:

   1. Copy the following application code and paste it into the editor:

      ```
      CREATE OR REPLACE STREAM DESTINATION_SQL_STREAM (
        "TICKER" VARCHAR(4), 
        "MOST_FREQUENT_VALUES" BIGINT
      );
      
      CREATE OR REPLACE PUMP "STREAM_PUMP" AS 
          INSERT INTO "DESTINATION_SQL_STREAM"
          SELECT STREAM * 
              FROM TABLE (TOP_K_ITEMS_TUMBLING(
                  CURSOR(SELECT STREAM * FROM "SOURCE_SQL_STREAM_001"),
                  'TICKER',         -- name of column in single quotes
                  5,                       -- number of the most frequently occurring values
                  60                       -- tumbling window size in seconds
                  )
              );
      ```

   1. Choose **Save and run SQL**\. 

      On the **Real\-time analytics **tab, you can see all the in\-application streams that the application created and verify the data\. 