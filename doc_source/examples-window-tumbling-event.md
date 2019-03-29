# Example: Tumbling Window Using an Event Timestamp<a name="examples-window-tumbling-event"></a>

When a windowed query processes each window in a non\-overlapping manner, the window is referred to as a *tumbling window*\. For details, see [Tumbling Windows \(Aggregations Using GROUP BY\)](tumbling-window-concepts.md)\. This Amazon Kinesis Data Analytics example demonstrates a tumbling window that uses an event timestamp, which is a user\-created timestamp that is included in the streaming data\. It uses this approach rather than just using ROWTIME, which is a timestamp that Kinesis Data Analytics creates when the application receives the record\. You would use an event timestamp in the streaming data if you want to create an aggregation based on when an event occurred, rather than when it was received by the application\. In this example, the `ROWTIME` value triggers the aggregation every minute, and the records are aggregated by both `ROWTIME` and the included event time\. 

In this example, you write the following records to an Amazon Kinesis stream\. The `EVENT_TIME` value is set to 5 seconds in the past, to simulate processing and transmission lag that might create a delay from when the event occurred, to when the record is ingested into Kinesis Data Analytics\.

```
{"EVENT_TIME": "2018-06-13T14:11:05.766191", "TICKER": "TBV", "PRICE": 43.65}
{"EVENT_TIME": "2018-06-13T14:11:05.848967", "TICKER": "AMZN", "PRICE": 35.61}
{"EVENT_TIME": "2018-06-13T14:11:05.931871", "TICKER": "MSFT", "PRICE": 73.48}
{"EVENT_TIME": "2018-06-13T14:11:06.014845", "TICKER": "AMZN", "PRICE": 18.64}
...
```

You then create a Kinesis Data Analytics application in the AWS Management Console, with the Kinesis data stream as the streaming source\. The discovery process reads sample records on the streaming source and infers an in\-application schema with three columns \(`EVENT_TIME`, `TICKER`, and `PRICE`\) as shown following\.

![\[Console screenshot showing the in-application schema with event time, ticker, and price columns.\]](http://docs.aws.amazon.com/kinesisanalytics/latest/dev/images/ex_tumbling_event_schema.png)

You use the application code with the `MIN` and `MAX` functions to create a windowed aggregation of the data\. Then you insert the resulting data into another in\-application stream, as shown in the following screenshot: 

![\[Console screenshot showing the resulting data in an in-application stream.\]](http://docs.aws.amazon.com/kinesisanalytics/latest/dev/images/ex_tumbling_event.png)

In the following procedure, you create a Kinesis Data Analytics application that aggregates values in the input stream in a tumbling window based on an event time\.

**Topics**
+ [Step 1: Create a Kinesis Data Stream](#examples-window-tumbling-event-1)
+ [Step 2: Create the Kinesis Data Analytics Application](#examples-window-tumbling-event-2)

## Step 1: Create a Kinesis Data Stream<a name="examples-window-tumbling-event-1"></a>

Create an Amazon Kinesis data stream and populate the records as follows:

1. Sign in to the AWS Management Console and open the Kinesis console at [https://console\.aws\.amazon\.com/kinesis](https://console.aws.amazon.com/kinesis)\.

1. Choose **Data Streams** in the navigation pane\.

1. Choose **Create Kinesis stream**, and then create a stream with one shard\. For more information, see [Create a Stream](https://docs.aws.amazon.com/streams/latest/dev/learning-kinesis-module-one-create-stream.html) in the *Amazon Kinesis Data Streams Developer Guide*\.

1. To write records to a Kinesis data stream in a production environment, we recommend using either the [Kinesis Client Library](https://docs.aws.amazon.com/streams/latest/dev/developing-producers-with-kpl.html) or [Kinesis Data Streams API](https://docs.aws.amazon.com/streams/latest/dev/developing-producers-with-sdk.html)\. For simplicity, this example uses the following Python script to generate records\. Run the code to populate the sample ticker records\. This simple code continuously writes a random ticker record to the stream\. Keep the script running so that you can generate the application schema in a later step\.

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

## Step 2: Create the Kinesis Data Analytics Application<a name="examples-window-tumbling-event-2"></a>

Create a Kinesis Data Analytics application as follows:

1. Open the Kinesis Data Analytics console at [ https://console\.aws\.amazon\.com/kinesisanalytics](https://console.aws.amazon.com/kinesisanalytics)\.

1. Choose **Create application**, enter an application name, and choose **Create application**\.

1. On the application details page, choose **Connect streaming data** to connect to the source\. 

1. On the **Connect to source** page, do the following:

   1. Choose the stream that you created in the preceding section\. 

   1. Choose **Discover Schema**\. Wait for the console to show the inferred schema and samples records that are used to infer the schema for the in\-application stream created\. The inferred schema has three columns\.

   1. Choose **Edit Schema**\. Change the **Column type** of the **EVENT\_TIME** column to `TIMESTAMP`\.

   1. Choose **Save schema and update stream samples**\. After the console saves the schema, choose **Exit**\.

   1. Choose **Save and continue**\.

1. On the application details page, choose **Go to SQL editor**\. To start the application, choose **Yes, start application** in the dialog box that appears\.

1. In the SQL editor, write the application code, and verify the results as follows:

   1. Copy the following application code and paste it into the editor\.

      ```
      CREATE OR REPLACE STREAM "DESTINATION_SQL_STREAM" (EVENT_TIME timestamp, TICKER VARCHAR(4), min_price REAL, max_price REAL);
      
      CREATE OR REPLACE PUMP "STREAM_PUMP" AS 
        INSERT INTO "DESTINATION_SQL_STREAM" 
          SELECT STREAM STEP("SOURCE_SQL_STREAM_001".EVENT_TIME BY INTERVAL '60' SECOND),
              TICKER,
               MIN(PRICE) AS MIN_PRICE,
               MAX(PRICE) AS MAX_PRICE
          FROM    "SOURCE_SQL_STREAM_001"
          GROUP BY TICKER, 
                   STEP("SOURCE_SQL_STREAM_001".ROWTIME BY INTERVAL '60' SECOND), 
                   STEP("SOURCE_SQL_STREAM_001".EVENT_TIME BY INTERVAL '60' SECOND);
      ```

   1. Choose **Save and run SQL**\. 

      On the **Real\-time analytics **tab, you can see all the in\-application streams that the application created and verify the data\. 