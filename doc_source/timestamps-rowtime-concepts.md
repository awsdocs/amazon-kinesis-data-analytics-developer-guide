# Timestamps and the ROWTIME Column<a name="timestamps-rowtime-concepts"></a>

In\-application streams include a special column called `ROWTIME`\. It stores a timestamp when Amazon Kinesis Data Analytics inserts a row in the first in\-application stream\. `ROWTIME` reflects the timestamp at which Amazon Kinesis Data Analytics inserted a record into the first in\-application stream after reading from the streaming source\. This `ROWTIME` value is then maintained throughout your application\. 

**Note**  
When you pump records from one in\-application stream into another, you don't need to explicitly copy the `ROWTIME` column, Amazon Kinesis Data Analytics copies this column for you\.

Amazon Kinesis Data Analytics guarantees that the `ROWTIME` values are monotonically increased\. You use this timestamp in time\-based windowed queries\. For more information, see [Windowed Queries](windowed-sql.md)\.

You can access the ROWTIME column in your `SELECT` statement like any other columns in your in\-application stream\. For example:

```
SELECT STREAM ROWTIME, 
              some_col_1, 
              some_col_2
FROM  SOURCE_SQL_STREAM_001
```

## Understanding Various Times in Streaming Analytics<a name="out-of-order-rows"></a>

In addition to `ROWTIME`, there are other types of times in real\-time streaming applications\. These are:
+ **Event time** – The timestamp when the event occurred\. This is also sometimes called the *client\-side time*\. It is often desirable to use this time in analytics because it is the time when an event occurred\. However, many event sources, such as mobile phones and web clients, do not have reliable clocks, which can lead to inaccurate times\. In addition, connectivity issues can lead to records appearing on a stream not in the same order the events occurred\.

   
+ **Ingest time** – The timestamp of when record was added to the streaming source\. Amazon Kinesis Data Streams includes a field called `APPROXIMATE_ARRIVAL_TIME` in every record that provides this timestamp\. This is also sometimes referred to as the *server\-side time*\. This ingest time is often the close approximation of event time\. If there is any kind of delay in the record ingestion to the stream, this can lead to inaccuracies, which are typically rare\. Also, the ingest time is rarely out of order, but it can occur due to the distributed nature of streaming data\. Therefore, Ingest time is a mostly accurate and in\-order reflection of the event time\. 

   
+ **Processing time** – The timestamp when Amazon Kinesis Data Analytics inserts a row in the first in\-application stream\. Amazon Kinesis Data Analytics provides this timestamp in the `ROWTIME` column that exists in each in\-application stream\.  The processing time is always monotonically increasing\. But it will not be accurate if your application falls behind\. \(If an application falls behind, the processing time does not accurately reflect the event time\.\) This `ROWTIME` is accurate in relation to the wall clock, but it might not be the time when the event actually occurred\. 

Using each of these times in windowed queries that are time\-based has advantages and disadvantages\. We recommend that you choose one or more of these times, and a strategy to deal with the relevant disadvantages based on your use case scenario\. 

**Note**  
If you are using row\-based windows, time is not an issue and you can ignore this section\.

We recommend a two\-window strategy that uses two time\-based, both `ROWTIME` and one of the other times \(ingest or event time\)\. 
+ Use `ROWTIME` as the first window, which controls how frequently the query emits the results, as shown in the following example\. It is not used as a logical time\.
+ Use one of the other times that is the logical time that you want to associate with your analytics\. This time represents when the event occurred\. In the following example, the analytics goal is to group the records and return count by ticker\.

The advantage of this strategy is that it can use a time that represents when the event occurred\. It can gracefully handle when your application falls behind or when events arrive out of order\. If the application falls behind when bringing records into the in\-application stream, they are still grouped by the logical time in the second window\. The query uses `ROWTIME` to guarantee the order of processing\. Any records that are late \(the ingest timestamp shows an earlier value compared to the `ROWTIME` value\) are also processed successfully\.

Consider the following query against the demo stream used in the [Getting Started Exercise](https://docs.aws.amazon.com/kinesisanalytics/latest/dev/get-started-exercise.html)\. The query uses the `GROUP BY` clause and emits a ticker count in a one\-minute tumbling window\. 

```
CREATE OR REPLACE STREAM "DESTINATION_SQL_STREAM" 
    ("ingest_time"    timestamp,
    "APPROXIMATE_ARRIVAL_TIME" timestamp,
    "ticker_symbol"  VARCHAR(12), 
    "symbol_count"        integer);
            
            
CREATE OR REPLACE PUMP "STREAM_PUMP" AS
    INSERT INTO "DESTINATION_SQL_STREAM"
    SELECT STREAM STEP("SOURCE_SQL_STREAM_001".ROWTIME BY INTERVAL '60' SECOND) AS "ingest_time",
        STEP("SOURCE_SQL_STREAM_001".APPROXIMATE_ARRIVAL_TIME BY INTERVAL '60' SECOND) AS "APPROXIMATE_ARRIVAL_TIME",
        "TICKER_SYMBOL",
        COUNT(*) AS "symbol_count"
    FROM "SOURCE_SQL_STREAM_001"
    GROUP BY "TICKER_SYMBOL",
        STEP("SOURCE_SQL_STREAM_001".ROWTIME BY INTERVAL '60' SECOND),
        STEP("SOURCE_SQL_STREAM_001".APPROXIMATE_ARRIVAL_TIME BY INTERVAL '60' SECOND);
```

In `GROUP BY`, you first group the records based on `ROWTIME` in a one\-minute window and then by `APPROXIMATE_ARRIVAL_TIME`\.

The timestamp values in the result are rounded down to the nearest 60\-second interval\. The first group result emitted by the query shows records in the first minute\. The second group of results emitted shows records in the next minutes based on `ROWTIME`\. The last record indicates that the application was late in bringing the record in the in\-application stream \(it shows a late `ROWTIME` value compared to the ingest timestamp\)\.

```
ROWTIME                  INGEST_TIME     TICKER_SYMBOL  SYMBOL_COUNT

--First one minute window.
2016-07-19 17:05:00.0    2016-07-19 17:05:00.0    ABC      10
2016-07-19 17:05:00.0    2016-07-19 17:05:00.0    DEF      15
2016-07-19 17:05:00.0    2016-07-19 17:05:00.0    XYZ      6
–-Second one minute window.
2016-07-19 17:06:00.0    2016-07-19 17:06:00.0    ABC      11
2016-07-19 17:06:00.0    2016-07-19 17:06:00.0    DEF      11
2016-07-19 17:06:00.0    2016-07-19 17:05:00.0    XYZ      1  *** 

***late-arriving record, instead of appearing in the result of the 
first 1-minute windows (based on ingest_time, it is in the result 
of the second 1-minute window.
```

You can combine the results for a final accurate count per minute by pushing the results to a downstream database\. For example, you can configure the application output to persist the results to a Kinesis Data Firehose delivery stream that can write to an Amazon Redshift table\. After results are in an Amazon Redshift table, you can query the table to compute the total count group by `Ticker_Symbol`\. In the case of `XYZ`, the total is accurate \(6\+1\) even though a record arrived late\.