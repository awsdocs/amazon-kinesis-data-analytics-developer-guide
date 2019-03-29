# Example: Stagger Window<a name="examples-window-stagger"></a>

When a windowed query processes separate windows for each unique partition key, starting when data with the matching key arrives, the window is referred to as a *stagger window*\. For details, see [Stagger Windows](stagger-window-concepts.md)\. This Amazon Kinesis Data Analytics example uses the EVENT\_TIME and TICKER columns to create stagger windows\. The source stream contains groups of six records with identical EVENT\_TIME and TICKER values that arrive within in a one\-minute period, but not necessarily with the same minute value \(for example, `18:41:xx`\)\.

In this example, you write the following records to a Kinesis data stream at the following times\. The script does not write the times to the stream, but the time that the record is ingested by the application is written to the `ROWTIME` field:

```
{"EVENT_TIME": "2018-08-01T20:17:20.797945", "TICKER": "AMZN"}   20:17:30
{"EVENT_TIME": "2018-08-01T20:17:20.797945", "TICKER": "AMZN"}   20:17:40
{"EVENT_TIME": "2018-08-01T20:17:20.797945", "TICKER": "AMZN"}   20:17:50
{"EVENT_TIME": "2018-08-01T20:17:20.797945", "TICKER": "AMZN"}   20:18:00
{"EVENT_TIME": "2018-08-01T20:17:20.797945", "TICKER": "AMZN"}   20:18:10
{"EVENT_TIME": "2018-08-01T20:17:20.797945", "TICKER": "AMZN"}   20:18:21
{"EVENT_TIME": "2018-08-01T20:18:21.043084", "TICKER": "INTC"}   20:18:31
{"EVENT_TIME": "2018-08-01T20:18:21.043084", "TICKER": "INTC"}   20:18:41
{"EVENT_TIME": "2018-08-01T20:18:21.043084", "TICKER": "INTC"}   20:18:51
{"EVENT_TIME": "2018-08-01T20:18:21.043084", "TICKER": "INTC"}   20:19:01
{"EVENT_TIME": "2018-08-01T20:18:21.043084", "TICKER": "INTC"}   20:19:11
{"EVENT_TIME": "2018-08-01T20:18:21.043084", "TICKER": "INTC"}   20:19:21
...
```

You then create a Kinesis Data Analytics application in the AWS Management Console, with the Kinesis data stream as the streaming source\. The discovery process reads sample records on the streaming source and infers an in\-application schema with two columns \(`EVENT_TIME` and `TICKER`\) as shown following\.

![\[Console screenshot showing the in-application schema with price and ticker columns.\]](http://docs.aws.amazon.com/kinesisanalytics/latest/dev/images/ex_stagger_schema.png)

You use the application code with the `COUNT` function to create a windowed aggregation of the data\. Then you insert the resulting data into another in\-application stream, as shown in the following screenshot: 

![\[Console screenshot showing the resulting data in an in-application stream.\]](http://docs.aws.amazon.com/kinesisanalytics/latest/dev/images/ex_stagger.png)

In the following procedure, you create a Kinesis Data Analytics application that aggregates values in the input stream in a stagger window based on EVENT\_TIME and TICKER\.

**Topics**
+ [Step 1: Create a Kinesis Data Stream](#examples-stagger-window-1)
+ [Step 2: Create the Kinesis Data Analytics Application](#examples-stagger-window-2)

## Step 1: Create a Kinesis Data Stream<a name="examples-stagger-window-1"></a>

Create an Amazon Kinesis data stream and populate the records as follows:

1. Sign in to the AWS Management Console and open the Kinesis console at [https://console\.aws\.amazon\.com/kinesis](https://console.aws.amazon.com/kinesis)\.

1. Choose **Data Streams** in the navigation pane\.

1. Choose **Create Kinesis stream**, and then create a stream with one shard\. For more information, see [Create a Stream](https://docs.aws.amazon.com/streams/latest/dev/learning-kinesis-module-one-create-stream.html) in the *Amazon Kinesis Data Streams Developer Guide*\.

1. To write records to a Kinesis data stream in a production environment, we recommend using either the [Kinesis Producer Library](https://docs.aws.amazon.com/streams/latest/dev/developing-producers-with-kpl.html) or [Kinesis Data Streams API](https://docs.aws.amazon.com/streams/latest/dev/developing-producers-with-sdk.html)\. For simplicity, this example uses the following Python script to generate records\. Run the code to populate the sample ticker records\. This simple code continuously writes a group of six records with the same random `EVENT_TIME` and ticker symbol to the stream, over the course of one minute\. Keep the script running so that you can generate the application schema in a later step\.

   ```
    
   import json
   import boto3
   import random
   import datetime
   import time
   
   kinesis = boto3.client('kinesis')
   def getData():
       data = {}
       now = datetime.datetime.utcnow() - datetime.timedelta(seconds=10)
       str_now = now.isoformat()
       data['EVENT_TIME'] = str_now
       data['TICKER'] = random.choice(['AAPL', 'AMZN', 'MSFT', 'INTC', 'TBV'])
       return data
   
   while True:
       data = json.dumps(getData())
       # Send six records, ten seconds apart, with the same event time and ticker
       for x in range(0, 6):
           print(data)
           kinesis.put_record(
                   StreamName="ExampleInputStream",
                   Data=data,
                   PartitionKey="partitionkey")
           time.sleep(10)
   ```

## Step 2: Create the Kinesis Data Analytics Application<a name="examples-stagger-window-2"></a>

Create a Kinesis Data Analytics application as follows:

1. Open the Kinesis Data Analytics console at [ https://console\.aws\.amazon\.com/kinesisanalytics](https://console.aws.amazon.com/kinesisanalytics)\.

1. Choose **Create application**, type an application name, and choose **Create application**\.

1. On the application details page, choose **Connect streaming data** to connect to the source\. 

1. On the **Connect to source** page, do the following:

   1. Choose the stream that you created in the preceding section\. 

   1. Choose **Discover Schema**\. Wait for the console to show the inferred schema and samples records that are used to infer the schema for the in\-application stream created\. The inferred schema has two columns\.

   1. Choose **Edit Schema**\. Change the **Column type** of the **EVENT\_TIME** column to `TIMESTAMP`\.

   1. Choose **Save schema and update stream samples**\. After the console saves the schema, choose **Exit**\.

   1. Choose **Save and continue**\.

1. On the application details page, choose **Go to SQL editor**\. To start the application, choose **Yes, start application** in the dialog box that appears\.

1. In the SQL editor, write the application code, and verify the results as follows:

   1. Copy the following application code and paste it into the editor\.

      ```
      CREATE OR REPLACE STREAM "DESTINATION_SQL_STREAM" (
          event_time TIMESTAMP,
          ticker_symbol    VARCHAR(4),
          ticker_count     INTEGER);
      
      CREATE OR REPLACE PUMP "STREAM_PUMP" AS 
        INSERT INTO "DESTINATION_SQL_STREAM" 
          SELECT STREAM 
              EVENT_TIME, 
              TICKER,
              COUNT(TICKER) AS ticker_count
          FROM "SOURCE_SQL_STREAM_001"
          WINDOWED BY STAGGER (
                  PARTITION BY TICKER, EVENT_TIME RANGE INTERVAL '1' MINUTE);
      ```

   1. Choose **Save and run SQL**\. 

      On the **Real\-time analytics **tab, you can see all the in\-application streams that the application created and verify the data\. 