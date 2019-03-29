# Example: Transforming DateTime Values<a name="app-string-datetime-manipulation"></a>

Amazon Kinesis Data Analytics supports converting columns to time stamps\. For example, you might want to use your own time stamp as part of a `GROUP BY` clause as another time\-based window, in addition to the `ROWTIME` column\. Kinesis Data Analytics provides operations and SQL functions for working with date and time fields\. 
+ **Date and time operators** – You can perform arithmetic operations on dates, times, and interval data types\. For more information, see [Date, Timestamp, and Interval Operators](https://docs.aws.amazon.com/kinesisanalytics/latest/sqlref/sql-reference-date-timestamp-interval.html) in the *Amazon Kinesis Data Analytics SQL Reference*\.

   
+ **SQL Functions** – These include the following\. For more information, see [Date and Time Functions](https://docs.aws.amazon.com/kinesisanalytics/latest/sqlref/sql-reference-date-time-functions.html) in the *Amazon Kinesis Data Analytics SQL Reference*\. 
  + `EXTRACT()` – Extracts one field from a date, time, time stamp, or interval expression\.
  + `CURRENT_TIME` – Returns the time when the query executes \(UTC\)\.
  + `CURRENT_DATE` – Returns the date when the query executes \(UTC\)\.
  + `CURRENT_TIMESTAMP` – Returns the time stamp when the query executes \(UTC\)\.
  + `LOCALTIME` – Returns the current time when the query executes as defined by the environment on which Kinesis Data Analytics is running \(UTC\)\.
  + `LOCALTIMESTAMP` – Returns the current time stamp as defined by the environment on which Kinesis Data Analytics is running \(UTC\)\.

     
+ **SQL Extensions** – These include the following\. For more information, see [Date and Time Functions](https://docs.aws.amazon.com/kinesisanalytics/latest/sqlref/sql-reference-date-time-functions.html) and [Datetime Conversion Functions](https://docs.aws.amazon.com/kinesisanalytics/latest/sqlref/sql-reference-datetime-conversion-functions.html) in the *Amazon Kinesis Data Analytics SQL Reference*\. 
  + `CURRENT_ROW_TIMESTAMP` – Returns a new time stamp for each row in the stream\. 
  + `TSDIFF` – Returns the difference of two time stamps in milliseconds\.
  + `CHAR_TO_DATE` – Converts a string to a date\.
  + `CHAR_TO_TIME` – Converts a string to time\.
  + `CHAR_TO_TIMESTAMP` – Converts a string to a time stamp\.
  + `DATE_TO_CHAR` – Converts a date to a string\.
  + `TIME_TO_CHAR` – Converts a time to a string\.
  + `TIMESTAMP_TO_CHAR` – Converts a time stamp to a string\.

Most of the preceding SQL functions use a format to convert the columns\. The format is flexible\. For example, you can specify the format `yyyy-MM-dd hh:mm:ss` to convert an input string `2009-09-16 03:15:24` into a time stamp\. For more information, [Char To Timestamp\(Sys\)](https://docs.aws.amazon.com/kinesisanalytics/latest/sqlref/sql-reference-char-to-timestamp.html) in the *Amazon Kinesis Data Analytics SQL Reference*\. 

## Example: Transforming Dates<a name="examples-transforming-dates"></a>

In this example, you write the following records to an Amazon Kinesis data stream\. 

```
{"EVENT_TIME": "2018-05-09T12:50:41.337510", "TICKER": "AAPL"}
{"EVENT_TIME": "2018-05-09T12:50:41.427227", "TICKER": "MSFT"}
{"EVENT_TIME": "2018-05-09T12:50:41.520549", "TICKER": "INTC"}
{"EVENT_TIME": "2018-05-09T12:50:41.610145", "TICKER": "MSFT"}
{"EVENT_TIME": "2018-05-09T12:50:41.704395", "TICKER": "AAPL"}
...
```

You then create an Amazon Kinesis data analytics application on the console, with the Kinesis stream as the streaming source\. The discovery process reads sample records on the streaming source and infers an in\-application schema with two columns \(`EVENT_TIME` and `TICKER`\) as shown\.

![\[Console screenshot showing the in-application schema with event time and ticker columns..\]](http://docs.aws.amazon.com/kinesisanalytics/latest/dev/images/ex_datetime_convert_0.png)

Then, you use the application code with SQL functions to convert the `EVENT_TIME` time stamp field in various ways\. You then insert the resulting data into another in\-application stream, as shown in the following screenshot: 

![\[Console screenshot showing the resulting data in an in-application stream..\]](http://docs.aws.amazon.com/kinesisanalytics/latest/dev/images/ex_datetime_convert_1.png)

### Step 1: Create a Kinesis Data Stream<a name="examples-transforming-dates-1"></a>

Create an Amazon Kinesis data stream and populate it with event time and ticker records as follows:

1. Sign in to the AWS Management Console and open the Kinesis console at [https://console\.aws\.amazon\.com/kinesis](https://console.aws.amazon.com/kinesis)\.

1. Choose **Data Streams** in the navigation pane\.

1. Choose **Create Kinesis stream**, and create a stream with one shard\.

1. Run the following Python code to populate the stream with sample data\. This simple code continuously writes a record with a random ticker symbol and the current time stamp to the stream\.

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

### Step 2: Create the Amazon Kinesis Data Analytics Application<a name="examples-transforming-dates-2"></a>

Create an application as follows:

1. Open the Kinesis Data Analytics console at [ https://console\.aws\.amazon\.com/kinesisanalytics](https://console.aws.amazon.com/kinesisanalytics)\.

1. Choose **Create application**, type an application name, and choose **Create application**\.

1. On the application details page, choose **Connect streaming data** to connect to the source\.

1. On the **Connect to source** page, do the following:

   1. Choose the stream that you created in the preceding section\. 

   1. Choose to create an IAM role\.

   1. Choose **Discover schema**\. Wait for the console to show the inferred schema and the sample records that are used to infer the schema for the in\-application stream created\. The inferred schema has two columns\.

   1. Choose **Edit Schema**\. Change the **Column type** of the **EVENT\_TIME** column to `TIMESTAMP`\.

   1. Choose **Save schema and update stream samples**\. After the console saves the schema, choose **Exit**\.

   1. Choose **Save and continue**\.

1. On the application details page, choose **Go to SQL editor**\. To start the application, choose **Yes, start application** in the dialog box that appears\.

1. In the SQL editor, write the application code and verify the results as follows:

   1. Copy the following application code and paste it into the editor\.

      ```
      CREATE OR REPLACE STREAM "DESTINATION_SQL_STREAM" (
          TICKER VARCHAR(4), 
          event_time TIMESTAMP, 
          five_minutes_before TIMESTAMP, 
          event_unix_timestamp BIGINT,
          event_timestamp_as_char VARCHAR(50),
          event_second INTEGER);
      
      CREATE OR REPLACE PUMP "STREAM_PUMP" AS INSERT INTO "DESTINATION_SQL_STREAM"
      
      SELECT STREAM 
          TICKER, 
          EVENT_TIME,
          EVENT_TIME - INTERVAL '5' MINUTE,
          UNIX_TIMESTAMP(EVENT_TIME),
          TIMESTAMP_TO_CHAR('yyyy-MM-dd hh:mm:ss', EVENT_TIME),
          EXTRACT(SECOND FROM EVENT_TIME) 
      FROM "SOURCE_SQL_STREAM_001"
      ```

   1. Choose **Save and run SQL**\. On the **Real\-time analytics **tab, you can see all the in\-application streams that the application created and verify the data\. 