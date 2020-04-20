# Continuous Queries<a name="continuous-queries-concepts"></a>

A query over a stream executes continuously over streaming data\. This continuous execution enables scenarios, such as the ability for applications to continuously query a stream and generate alerts\. 

In the Getting Started exercise, you have an in\-application stream named `SOURCE_SQL_STREAM_001`\. It continuously receives stock prices from a demo stream \(a Kinesis data stream\)\. The schema is as follows:

```
(TICKER_SYMBOL VARCHAR(4), 
 SECTOR varchar(16), 
 CHANGE REAL, 
 PRICE REAL)
```

Suppose that you are interested in stock price changes greater than 15 percent\. You can use the following query in your application code\. This query runs continuously and emits records when a stock price change greater than 15 percent is detected\.

```
SELECT STREAM TICKER_SYMBOL, PRICE 
      FROM   "SOURCE_SQL_STREAM_001"
      WHERE  (ABS((CHANGE / (PRICE-CHANGE)) * 100)) > 15
```

Use the following procedure to set up an Amazon Kinesis Data Analytics application and test this query\.

**To test the query**

1. Create an application by following the [Getting Started Exercise](https://docs.aws.amazon.com/kinesisanalytics/latest/dev/get-started-exercise.html)\.

1. Replace the `SELECT` statement in the application code with the preceding `SELECT` query\. The resulting application code is shown following:

   ```
   CREATE OR REPLACE STREAM "DESTINATION_SQL_STREAM" (ticker_symbol VARCHAR(4), 
                                                      price DOUBLE);
   -- CREATE OR REPLACE PUMP to insert into output
   CREATE OR REPLACE PUMP "STREAM_PUMP" AS 
     INSERT INTO "DESTINATION_SQL_STREAM" 
         SELECT STREAM TICKER_SYMBOL, 
                       PRICE 
         FROM   "SOURCE_SQL_STREAM_001"
         WHERE  (ABS((CHANGE / (PRICE-CHANGE)) * 100)) > 15;
   ```