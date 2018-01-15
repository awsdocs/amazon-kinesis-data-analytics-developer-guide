# Example: Throttled Alerts<a name="app-throttled-alerts"></a>

In this application, the query runs continuously on the in\-application stream created over the demo stream\. For more information, see [Continuous Queries](continuous-queries-concepts.md)\. If any rows show stock price change is greater than 1%, those rows are inserted in another in\-application stream\. The application throttles the alerts such that an alert is sent immediately when the stock price changes, but no more than one alert per minute per stock symbol is sent to the in\-application stream\.

**To create a throttled alerts application**

1. Create the Amazon Kinesis Data Analytics application as described in the [Getting Started Exercise](http://docs.aws.amazon.com/kinesisanalytics/latest/dev/get-started-exercise.html)\.

1. In the SQL editor, replace the application code with the following: 

   ```
   CREATE OR REPLACE STREAM "CHANGE_STREAM" 
              (ticker_symbol VARCHAR(4), 
               sector        VARCHAR(12), 
               change        DOUBLE, 
               price         DOUBLE);
   
   CREATE OR REPLACE PUMP "change_pump" AS 
      INSERT INTO "CHANGE_STREAM"
         SELECT STREAM ticker_symbol, sector, change, price 
         FROM   "SOURCE_SQL_STREAM_001"
         WHERE  (ABS(Change / (Price - Change)) * 100) > 1;
         
   -- ** Trigger Count and Limit **
   -- Counts "triggers" or those values that evaluated true against the previous where clause
   -- Then provides its own limit on the number of triggers per hour per ticker symbol to what
   -- is specified in the WHERE clause
   
   CREATE OR REPLACE STREAM TRIGGER_COUNT_STREAM (
      ticker_symbol VARCHAR(4), 
      change REAL,
      trigger_count INTEGER);
   
   CREATE OR REPLACE PUMP trigger_count_pump AS INSERT INTO TRIGGER_COUNT_STREAM
   SELECT STREAM ticker_symbol, change, trigger_count
   FROM (
       SELECT STREAM ticker_symbol, change, COUNT(*) OVER W1 as trigger_count
       FROM "CHANGE_STREAM"
       --window to perform aggregations over last minute to keep track of triggers
       WINDOW W1 AS (PARTITION BY ticker_symbol RANGE INTERVAL '1' MINUTE PRECEDING)
   )
   WHERE trigger_count >= 1;
   ```

   The SELECT statement in the application code filters rows in the `SOURCE_SQL_STREAM_001` for stock price changes greater than 1%, and inserts those rows to another in\-application stream `CHANGE_STREAM` using a pump\. 

   The application then creates a second stream called `TRIGGER_COUNT_STREAM` for the throttled alerts\. A second query selects records from a window that hops forward every time a record is admitted into it, such that only one record per stock ticker per minute is written to the stream\.

1. Click **Save and run SQL**\.

The preceding example outputs a stream to `TRIGGER_COUNT_STREAM` similar to the following:

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/kinesisanalytics/latest/dev/images/ex-throttle-alerts.png)