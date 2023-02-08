# Migrating to Kinesis Data Analytics for Apache Flink: Examples<a name="examples-migrating-to-kda-studio"></a>

**Warning**  
For new projects, we recommend that you use the new Kinesis Data Analytics Studio over Kinesis Data Analytics for SQL Applications\. Kinesis Data Analytics Studio combines ease of use with advanced analytical capabilities, enabling you to build sophisticated stream processing applications in minutes\.

To migrate your workloads to Kinesis Data Analytics Studio or Kinesis Data Analytics for Apache Flink, this section provides query translations you can use for common use cases\.  

**Note**  
Kinesis Data Analytics for Apache Flink and Kinesis Data Analytics Studio offer advanced data stream processing features not available in SQL\-based Kinesis Data Analytics applications\. These include exactly\-once processing semantics, event\-time windows, extensibility using user defined functions and custom integrations, imperative language support, durable application state, horizontal scaling, support for multiple data sources, extensible integrations, and more\. These are critical for ensuring accuracy, completeness, consistency, and reliability of data stream processing\. 

 Before you explore these examples we recommend you first review [Using a Studio notebook with a Managed Service for Apache Flink](https://docs.aws.amazon.com/kinesisanalytics/latest/java/how-notebook.html)\. 

**Topics**
+ [Re\-creating Kinesis Data Analytics for SQL queries in Kinesis Data Analytics Studio](#examples-recreating-queries)

## Re\-creating Kinesis Data Analytics for SQL queries in Kinesis Data Analytics Studio<a name="examples-recreating-queries"></a>

The following table provides translations of common SQL\-based Kinesis Data Analytics application queries to Kinesis Data Analytics Studio\. 

### Multi\-Step application<a name="Multi-Step-application"></a>

------
#### [ SQL\-based Kinesis Data Analytics application  ]

```
CREATE 
OR REPLACE STREAM "IN_APP_STREAM_001" (
   ingest_time TIMESTAMP,
   ticker_symbol VARCHAR(4),
   sector VARCHAR(16), price REAL, change REAL);
CREATE 
OR REPLACE PUMP "STREAM_PUMP_001" AS 
INSERT INTO
   "IN_APP_STREAM_001"
   SELECT
      STREAM APPROXIMATE_ARRIVAL_TIME,
      ticker_symbol,
      sector,
      price,
      change FROM "SOURCE_SQL_STREAM_001";
-- Second in-app stream and pump
CREATE 
OR REPLACE STREAM "IN_APP_STREAM_02" (ingest_time TIMESTAMP,
   ticker_symbol VARCHAR(4),
   sector VARCHAR(16),
   price REAL,
   change REAL);
CREATE 
OR REPLACE PUMP "STREAM_PUMP_02" AS 
INSERT INTO
   "IN_APP_STREAM_02"
   SELECT
      STREAM ingest_time,
      ticker_symbol,
      sector,
      price,
      change FROM "IN_APP_STREAM_001";
-- Destination in-app stream and third pump
CREATE 
OR REPLACE STREAM "DESTINATION_SQL_STREAM" (ingest_time TIMESTAMP,
   ticker_symbol VARCHAR(4),
   sector VARCHAR(16),
   price REAL,
   change REAL);
CREATE 
OR REPLACE PUMP "STREAM_PUMP_03" AS 
INSERT INTO
   "DESTINATION_SQL_STREAM"
   SELECT
      STREAM ingest_time,
      ticker_symbol,
      sector,
      price,
      change FROM "IN_APP_STREAM_02";
```

------
#### [ Kinesis Data Analytics Studio ]

```
Query 1 - % flink.ssql DROP TABLE IF EXISTS SOURCE_SQL_STREAM_001;           
           
CREATE TABLE SOURCE_SQL_STREAM_001 (TICKER_SYMBOL VARCHAR(4),
   SECTOR VARCHAR(16),
   PRICE DOUBLE,
   CHANGE DOUBLE,
   APPROXIMATE_ARRIVAL_TIME TIMESTAMP(3) METADATA 

FROM
   'timestamp' VIRTUAL,
   WATERMARK FOR APPROXIMATE_ARRIVAL_TIME AS APPROXIMATE_ARRIVAL_TIME - INTERVAL '1' SECOND ) 
   PARTITIONED BY (TICKER_SYMBOL) WITH (
      'connector' = 'kinesis',
      'stream' = 'kinesis-analytics-demo-stream',
      'aws.region' = 'us-east-1',
      'scan.stream.initpos' = 'LATEST',
      'format' = 'json',
      'json.timestamp-format.standard' = 'ISO-8601');
DROP TABLE IF EXISTS IN_APP_STREAM_001;

CREATE TABLE IN_APP_STREAM_001 ( 
   INGEST_TIME TIMESTAMP,
   TICKER_SYMBOL VARCHAR(4),
   SECTOR VARCHAR(16),
   PRICE DOUBLE,
   CHANGE DOUBLE )
PARTITIONED BY (TICKER_SYMBOL) WITH ( 
      'connector' = 'kinesis', 
      'stream' = 'IN_APP_STREAM_001', 
      'aws.region' = 'us-east-1',
      'scan.stream.initpos' = 'LATEST',
      'format' = 'json',
      'json.timestamp-format.standard' = 'ISO-8601');
   
DROP TABLE IF EXISTS IN_APP_STREAM_02;

CREATE TABLE IN_APP_STREAM_02 (
   INGEST_TIME TIMESTAMP, 
   TICKER_SYMBOL VARCHAR(4),
   SECTOR VARCHAR(16),
   PRICE DOUBLE, 
   CHANGE DOUBLE ) 
PARTITIONED BY (TICKER_SYMBOL) WITH ( 
   'connector' = 'kinesis',
   'stream' = 'IN_APP_STREAM_02',   
   'aws.region' = 'us-east-1',
   'scan.stream.initpos' = 'LATEST',
   'format' = 'json',
   'json.timestamp-format.standard' = 'ISO-8601');
   
DROP TABLE IF EXISTS DESTINATION_SQL_STREAM;

CREATE TABLE DESTINATION_SQL_STREAM (
   INGEST_TIME TIMESTAMP, TICKER_SYMBOL VARCHAR(4), SECTOR VARCHAR(16), 
   PRICE DOUBLE, CHANGE DOUBLE )
PARTITIONED BY (TICKER_SYMBOL) WITH ( 
   'connector' = 'kinesis',
   'stream' = 'DESTINATION_SQL_STREAM',
   'aws.region' = 'us-east-1', 
   'scan.stream.initpos' = 'LATEST',  
   'format' = 'json',
   'json.timestamp-format.standard' = 'ISO-8601');


Query 2 - % flink.ssql(type = 
update
) 
   INSERT INTO
      IN_APP_STREAM_001 
      SELECT
         APPROXIMATE_ARRIVAL_TIME AS INGEST_TIME,
         TICKER_SYMBOL,
         SECTOR,
         PRICE,
         CHANGE 
      FROM
         SOURCE_SQL_STREAM_001;


Query 3 - % flink.ssql(type = 
update
) 
   INSERT INTO
      IN_APP_STREAM_02 
      SELECT
         INGEST_TIME,
         TICKER_SYMBOL,
         SECTOR,
         PRICE,
         CHANGE 
      FROM
         IN_APP_STREAM_001;


Query 4 - % flink.ssql(type = 
update
) 
   INSERT INTO
      DESTINATION_SQL_STREAM 
      SELECT
         INGEST_TIME,
         TICKER_SYMBOL,
         SECTOR,
         PRICE,
         CHANGE 
      FROM
         IN_APP_STREAM_02;
```

------

### Transforming DateTime values<a name="transform-date-time-values"></a>

------
#### [ SQL\-based Kinesis Data Analytics application  ]

```
CREATE 
OR REPLACE STREAM "DESTINATION_SQL_STREAM" (
   TICKER VARCHAR(4),
   event_time TIMESTAMP,
   five_minutes_before TIMESTAMP,
   event_unix_timestamp BIGINT, 
   event_timestamp_as_char VARCHAR(50),
   event_second INTEGER);
   
CREATE 
OR REPLACE PUMP "STREAM_PUMP" AS 
INSERT INTO
   "DESTINATION_SQL_STREAM"
   SELECT
      STREAM TICKER,
      EVENT_TIME,
      EVENT_TIME - INTERVAL '5' MINUTE,
      UNIX_TIMESTAMP(EVENT_TIME),
      TIMESTAMP_TO_CHAR('yyyy-MM-dd hh:mm:ss', EVENT_TIME),
      EXTRACT(SECOND 
   FROM
      EVENT_TIME) 
   FROM
      "SOURCE_SQL_STREAM_001"
```

------
#### [ Kinesis Data Analytics Studio  ]

```
Query 1 - % flink.ssql(type = 
update
) CREATE TABLE DESTINATION_SQL_STREAM (
   TICKER VARCHAR(4),
   EVENT_TIME TIMESTAMP(3),
   FIVE_MINUTES_BEFORE TIMESTAMP(3),   
   EVENT_UNIX_TIMESTAMP INT,
   EVENT_TIMESTAMP_AS_CHAR VARCHAR(50),
   EVENT_SECOND INT)
   
PARTITIONED BY (TICKER) WITH (
   'connector' = 'kinesis', 'stream' = 'kinesis-analytics-demo-stream',   
   'aws.region' = 'us-east-1',
   'scan.stream.initpos' = 'LATEST',
   'format' = 'json',
   'json.timestamp-format.standard' = 'ISO-8601')

Query 2 - % flink.ssql(type = 
   update
) 
      SELECT
         TICKER,
         EVENT_TIME,
         EVENT_TIME - INTERVAL '5' MINUTE AS FIVE_MINUTES_BEFORE,
         UNIX_TIMESTAMP() AS EVENT_UNIX_TIMESTAMP,
         DATE_FORMAT(EVENT_TIME, 'yyyy-MM-dd hh:mm:ss') AS EVENT_TIMESTAMP_AS_CHAR,
         EXTRACT(SECOND 
      FROM
         EVENT_TIME) AS EVENT_SECOND 
      FROM
         DESTINATION_SQL_STREAM;
```

------

### Simple alerts<a name="simple-alerts"></a>

------
#### [ SQL\-based Kinesis Data Analytics application  ]

```
CREATE 
OR REPLACE STREAM "DESTINATION_SQL_STREAM"(
   ticker_symbol VARCHAR(4),
   sector VARCHAR(12),
   change DOUBLE,
   price DOUBLE);
   
CREATE 
OR REPLACE PUMP "STREAM_PUMP" AS INSERT INTO "DESTINATION_SQL_STREAM"
SELECT
   STREAM ticker_symbol,
   sector,
   change,
   price 
FROM
   "SOURCE_SQL_STREAM_001"
WHERE
   (
      ABS(Change / (Price - Change)) * 100
   )
   > 1
```

------
#### [ Kinesis Data Analytics Studio  ]

```
Query 1 - % flink.ssql(type = 
update
) DROP TABLE IF EXISTS DESTINATION_SQL_STREAM;

CREATE TABLE DESTINATION_SQL_STREAM (
   TICKER_SYMBOL VARCHAR(4),
   SECTOR VARCHAR(4), 
   CHANGE DOUBLE,       
   PRICE DOUBLE ) 
PARTITIONED BY (TICKER_SYMBOL) WITH ( 
   'connector' = 'kinesis',
   'stream' = 'kinesis-analytics-demo-stream',
   'aws.region' = 'us-east-1',
   'scan.stream.initpos' = 'LATEST',
   'format' = 'json',
   'json.timestamp-format.standard' = 'ISO-8601');
   
Query 2 - % flink.ssql(type = 
update
) 
   SELECT
      TICKER_SYMBOL,
      SECTOR,
      CHANGE,
      PRICE 
   FROM
      DESTINATION_SQL_STREAM 
   WHERE
      (
         ABS(CHANGE / (PRICE - CHANGE)) * 100
      )
      > 1;
```

------

### Throttled alerts<a name="throttled-alerts"></a>

------
#### [ SQL\-based Kinesis Data Analytics application  ]

```
CREATE 
OR REPLACE STREAM "CHANGE_STREAM"(
   ticker_symbol VARCHAR(4),
   sector VARCHAR(12),
   change DOUBLE,
   price DOUBLE);
   
CREATE 
OR REPLACE PUMP "change_pump" AS INSERT INTO "CHANGE_STREAM"
SELECT
   STREAM ticker_symbol,
   sector,
   change,
   price
FROM "SOURCE_SQL_STREAM_001"
WHERE
   (
      ABS(Change / (Price - Change)) * 100
   )
   > 1;
-- ** Trigger Count and Limit **
-- Counts "triggers" or those values that evaluated true against the previous where clause
-- Then provides its own limit on the number of triggers per hour per ticker symbol to what is specified in the WHERE clause

CREATE 
OR REPLACE STREAM TRIGGER_COUNT_STREAM (
   ticker_symbol VARCHAR(4),
   change REAL,
   trigger_count INTEGER);
   
CREATE 
OR REPLACE PUMP trigger_count_pump AS 
INSERT INTO
   TRIGGER_COUNT_STREAMSELECT STREAM ticker_symbol,
   change,
   trigger_count 
FROM
   (
      SELECT
         STREAM ticker_symbol,
         change,
         COUNT(*) OVER W1 as trigger_countFROM "CHANGE_STREAM" --window to perform aggregations over last minute to keep track of triggers
         WINDOW W1 AS 
         (
            PARTITION BY ticker_symbol RANGE INTERVAL '1' MINUTE PRECEDING
         )
   )
WHERE
   trigger_count >= 1;
```

------
#### [ Kinesis Data Analytics Studio  ]

```
Query 1 - % flink.ssql(type = 
update
) DROP TABLE IF EXISTS DESTINATION_SQL_STREAM;

CREATE TABLE DESTINATION_SQL_STREAM (
   TICKER_SYMBOL VARCHAR(4),
   SECTOR VARCHAR(4),      
   CHANGE DOUBLE, PRICE DOUBLE,
   EVENT_TIME AS PROCTIME()) 
PARTITIONED BY (TICKER_SYMBOL) 
WITH (
   'connector' = 'kinesis',
   'stream' = 'kinesis-analytics-demo-stream',
   'aws.region' = 'us-east-1',
   'scan.stream.initpos' = 'LATEST',
   'format' = 'json',
   'json.timestamp-format.standard' = 'ISO-8601');   
DROP TABLE IF EXISTS TRIGGER_COUNT_STREAM;
CREATE TABLE TRIGGER_COUNT_STREAM ( 
   TICKER_SYMBOL VARCHAR(4), 
   CHANGE DOUBLE, 
   TRIGGER_COUNT INT) 
PARTITIONED BY (TICKER_SYMBOL);

Query 2 - % flink.ssql(type = 
update
) 
   SELECT
      TICKER_SYMBOL,
      SECTOR,
      CHANGE,
      PRICE 
   FROM
      DESTINATION_SQL_STREAM 
   WHERE
      (
         ABS(CHANGE / (PRICE - CHANGE)) * 100
      )
      > 1;
      
Query 3 - % flink.ssql(type = 
update
) 
   SELECT * 
   FROM(
         SELECT
            TICKER_SYMBOL,
            CHANGE,
            COUNT(*) AS TRIGGER_COUNT 
         FROM
            DESTINATION_SQL_STREAM 
         GROUP BY
            TUMBLE(EVENT_TIME, INTERVAL '1' MINUTE),
            TICKER_SYMBOL,
            CHANGE 
      )
   WHERE
      TRIGGER_COUNT > 1;
```

------

### Aggregating Partial Results from a Query<a name="aggregate-partial-results"></a>

------
#### [ SQL\-based Kinesis Data Analytics application  ]

```
CREATE 
OR REPLACE STREAM "CALC_COUNT_SQL_STREAM"(
   TICKER VARCHAR(4),
   TRADETIME TIMESTAMP,
   TICKERCOUNT DOUBLE);
   
CREATE 
OR REPLACE STREAM "DESTINATION_SQL_STREAM"(
   TICKER VARCHAR(4),
   TRADETIME TIMESTAMP,
   TICKERCOUNT DOUBLE);
   
CREATE PUMP "CALC_COUNT_SQL_PUMP_001" AS 
INSERT INTO
   "CALC_COUNT_SQL_STREAM"(
   "TICKER",
   "TRADETIME",
   "TICKERCOUNT") 
   SELECT
      STREAM "TICKER_SYMBOL",
      STEP("SOURCE_SQL_STREAM_001",
      "ROWTIME" BY INTERVAL '1' MINUTE) as "TradeTime",
      COUNT(*) AS "TickerCount "
   FROM
      "SOURCE_SQL_STREAM_001" 
   GROUP BY
      STEP("SOURCE_SQL_STREAM_001". ROWTIME BY INTERVAL '1' MINUTE),
      STEP("SOURCE_SQL_STREAM_001"." APPROXIMATE_ARRIVAL_TIME" BY INTERVAL '1' MINUTE),
      TICKER_SYMBOL;
CREATE PUMP "AGGREGATED_SQL_PUMP" AS 
INSERT INTO
   "DESTINATION_SQL_STREAM" (
   "TICKER",
   "TRADETIME",
   "TICKERCOUNT") 
   SELECT
      STREAM "TICKER",
      "TRADETIME",
      SUM("TICKERCOUNT") OVER W1 AS "TICKERCOUNT" 
   FROM
      "CALC_COUNT_SQL_STREAM" WINDOW W1 AS 
      (
         PARTITION BY "TRADETIME" RANGE INTERVAL '10' MINUTE PRECEDING
      )
;
```

------
#### [ Kinesis Data Analytics Studio  ]

```
Query 1 - % flink.ssql(type = 
update
) DROP TABLE IF EXISTS SOURCE_SQL_STREAM_001;
CREATE TABLE SOURCE_SQL_STREAM_001 (
   TICKER_SYMBOL VARCHAR(4),
   TRADETIME AS PROCTIME(),
   APPROXIMATE_ARRIVAL_TIME TIMESTAMP(3) METADATA 
FROM
   'timestamp' VIRTUAL,
   WATERMARK FOR APPROXIMATE_ARRIVAL_TIME AS APPROXIMATE_ARRIVAL_TIME - INTERVAL '1' SECOND) 
PARTITIONED BY (TICKER_SYMBOL) WITH (
   'connector' = 'kinesis',   
   'stream' = 'kinesis-analytics-demo-stream',
   'aws.region' = 'us-east-1',
   'scan.stream.initpos' = 'LATEST',
   'format' = 'json',
   'json.timestamp-format.standard' = 'ISO-8601');
DROP TABLE IF EXISTS CALC_COUNT_SQL_STREAM;
CREATE TABLE CALC_COUNT_SQL_STREAM (
   TICKER VARCHAR(4),
   TRADETIME TIMESTAMP(3),
   WATERMARK FOR TRADETIME AS TRADETIME - INTERVAL '1' SECOND,   
   TICKERCOUNT BIGINT NOT NULL ) PARTITIONED BY (TICKER) WITH ( 
      'connector' = 'kinesis',
      'stream' = 'CALC_COUNT_SQL_STREAM',
      'aws.region' = 'us-east-1',       
      'scan.stream.initpos' = 'LATEST',
      'format' = 'csv');
DROP TABLE IF EXISTS DESTINATION_SQL_STREAM;
CREATE TABLE DESTINATION_SQL_STREAM (
   TICKER VARCHAR(4),
   TRADETIME TIMESTAMP(3),
   WATERMARK FOR TRADETIME AS TRADETIME - INTERVAL '1' SECOND, 
   TICKERCOUNT BIGINT NOT NULL )
   PARTITIONED BY (TICKER) WITH ('connector' = 'kinesis', 
      'stream' = 'DESTINATION_SQL_STREAM',
      'aws.region' = 'us-east-1',
      'scan.stream.initpos' = 'LATEST',
      'format' = 'csv');

Query 2 - % flink.ssql(type = 
update
) 
   INSERT INTO
      CALC_COUNT_SQL_STREAM 
      SELECT
         TICKER,
         TO_TIMESTAMP(TRADETIME, 'yyyy-MM-dd HH:mm:ss') AS TRADETIME,
         TICKERCOUNT 
      FROM
         (
            SELECT
               TICKER_SYMBOL AS TICKER,
               DATE_FORMAT(TRADETIME, 'yyyy-MM-dd HH:mm:00') AS TRADETIME,
               COUNT(*) AS TICKERCOUNT 
            FROM
               SOURCE_SQL_STREAM_001 
            GROUP BY
               TUMBLE(TRADETIME, INTERVAL '1' MINUTE),
               DATE_FORMAT(TRADETIME, 'yyyy-MM-dd HH:mm:00'),
               DATE_FORMAT(APPROXIMATE_ARRIVAL_TIME, 'yyyy-MM-dd HH:mm:00'),
               TICKER_SYMBOL 
         )
;

Query 3 - % flink.ssql(type = 
update
) 
   SELECT
      * 
   FROM
      CALC_COUNT_SQL_STREAM;
      
Query 4 - % flink.ssql(type = 
update
) 
   INSERT INTO
      DESTINATION_SQL_STREAM 
      SELECT
         TICKER,
         TRADETIME,
         SUM(TICKERCOUNT) OVER W1 AS TICKERCOUNT 
      FROM
         CALC_COUNT_SQL_STREAM WINDOW W1 AS 
         (
            PARTITION BY TICKER 
         ORDER BY
            TRADETIME RANGE INTERVAL '10' MINUTE PRECEDING
         )
;

Query 5 - % flink.ssql(type = 
update
) 
   SELECT
      * 
   FROM
      DESTINATION_SQL_STREAM;
```

------

### Transforming string values<a name="transform-string-values"></a>

------
#### [ SQL\-based Kinesis Data Analytics application  ]

```
CREATE 
OR REPLACE STREAM for cleaned up referrerCREATE 
OR REPLACE STREAM "DESTINATION_SQL_STREAM" ( "ingest_time" TIMESTAMP, "referrer" VARCHAR(32));
CREATE 
OR REPLACE PUMP "myPUMP" AS INSERT INTO "DESTINATION_SQL_STREAM"
SELECT
   STREAM "APPROXIMATE_ARRIVAL_TIME",
   SUBSTRING("referrer", 12, 
   (
      POSITION('.com' IN "referrer") - POSITION('www.' IN "referrer") - 4
   )
) 
FROM
   "SOURCE_SQL_STREAM_001";
```

------
#### [ Kinesis Data Analytics Studio  ]

```
Query 1 - % flink.ssql(type = 
update
) CREATE TABLE DESTINATION_SQL_STREAM (
   referrer VARCHAR(32),
   ingest_time AS PROCTIME() ) PARTITIONED BY (referrer) 
WITH (
   'connector' = 'kinesis',
   'stream' = 'kinesis-analytics-demo-stream',
   'aws.region' = 'us-east-1',   
   'scan.stream.initpos' = 'LATEST',
   'format' = 'json',
   'json.timestamp-format.standard' = 'ISO-8601')

Query 2 - % flink.ssql(type = 
   update
) 
      SELECT
         ingest_time,
         substring(referrer, 12, 6) as referrer 
      FROM
         DESTINATION_SQL_STREAM;
```

------

### Replacing a substring using Regex<a name="substring-regex"></a>

------
#### [ SQL\-based Kinesis Data Analytics application  ]

```
CREATE 
OR REPLACE STREAM for cleaned up referrerCREATE 
OR REPLACE STREAM "DESTINATION_SQL_STREAM" ( "ingest_time" TIMESTAMP, "referrer" VARCHAR(32));
CREATE 
OR REPLACE PUMP "myPUMP" AS INSERT INTO "DESTINATION_SQL_STREAM"
SELECT
   STREAM "APPROXIMATE_ARRIVAL_TIME",
   REGEX_REPLACE("REFERRER", 'http://', 'https://', 1, 0) 
FROM
   "SOURCE_SQL_STREAM_001";
```

------
#### [ Kinesis Data Analytics Studio  ]

```
Query 1 - % flink.ssql(type = 
update
) CREATE TABLE DESTINATION_SQL_STREAM (
   referrer VARCHAR(32),
   ingest_time AS PROCTIME()) 
PARTITIONED BY (referrer) WITH (
   'connector' = 'kinesis',
   'stream' = 'kinesis-analytics-demo-stream',
   'aws.region' = 'us-east-1',
   'scan.stream.initpos' = 'LATEST',
   'format' = 'json',
   'json.timestamp-format.standard' = 'ISO-8601')

Query 2 - % flink.ssql(type = 
   update
) 
      SELECT
         ingest_time,
         REGEXP_REPLACE(referrer, 'http', 'https') as referrer 
      FROM
         DESTINATION_SQL_STREAM;
```

------

### Regex log parse<a name="regex-log-parse"></a>

------
#### [ SQL\-based Kinesis Data Analytics application  ]

```
CREATE 
OR REPLACE STREAM "DESTINATION_SQL_STREAM"(
   sector VARCHAR(24),
   match1 VARCHAR(24),
   match2 VARCHAR(24));
CREATE 
OR REPLACE PUMP "STREAM_PUMP" AS 
INSERT INTO
   "DESTINATION_SQL_STREAM" 
   SELECT
      STREAM T.SECTOR,
      T.REC.COLUMN1,
      T.REC.COLUMN2 
   FROM
      (
         SELECT
            STREAM SECTOR,
            REGEX_LOG_PARSE(SECTOR, '.*([E].).*([R].*)') AS REC 
         FROM
            SOURCE_SQL_STREAM_001
      )
      AS T;
```

------
#### [ Kinesis Data Analytics Studio  ]

```
Query 1 - % flink.ssql(type = 
update
) CREATE TABLE DESTINATION_SQL_STREAM (
   CHANGE DOUBLE, PRICE DOUBLE,
   TICKER_SYMBOL VARCHAR(4),
   SECTOR VARCHAR(16)) 
PARTITIONED BY (SECTOR) WITH (
   'connector' = 'kinesis',
   'stream' = 'kinesis-analytics-demo-stream',
   'aws.region' = 'us-east-1',   
   'scan.stream.initpos' = 'LATEST',
   'format' = 'json',
   'json.timestamp-format.standard' = 'ISO-8601')

Query 2 - % flink.ssql(type = 
   update
) 
SELECT
   * 
FROM
   (
      SELECT
         SECTOR,
         REGEXP_EXTRACT(SECTOR, '.([E].).([R].)', 1) AS MATCH1,
         REGEXP_EXTRACT(SECTOR, '.([E].).([R].)', 2) AS MATCH2 
      FROM
         DESTINATION_SQL_STREAM 
   )
WHERE
   MATCH1 IS NOT NULL 
   AND MATCH2 IS NOT NULL;
```

------

### Transforming DateTime values<a name="transform-date-time-values"></a>

------
#### [ SQL\-based Kinesis Data Analytics application  ]

```
CREATE 
OR REPLACE STREAM "DESTINATION_SQL_STREAM" ( 
   TICKER VARCHAR(4),
   event_time TIMESTAMP,
   five_minutes_before TIMESTAMP,
   event_unix_timestamp BIGINT,
   event_timestamp_as_char VARCHAR(50),
   event_second INTEGER);
CREATE 
OR REPLACE PUMP "STREAM_PUMP" AS 
INSERT INTO
   "DESTINATION_SQL_STREAM"
   SELECT
      STREAM TICKER,
      EVENT_TIME,
      EVENT_TIME - INTERVAL '5' MINUTE,
      UNIX_TIMESTAMP(EVENT_TIME),
      TIMESTAMP_TO_CHAR('yyyy-MM-dd hh:mm:ss', EVENT_TIME),
      EXTRACT(SECOND 
   FROM
      EVENT_TIME) 
   FROM
      "SOURCE_SQL_STREAM_001"
```

------
#### [ Kinesis Data Analytics Studio  ]

```
Query 1 - % flink.ssql(type = 
update
) CREATE TABLE DESTINATION_SQL_STREAM (
   TICKER VARCHAR(4),
   EVENT_TIME TIMESTAMP(3),
   FIVE_MINUTES_BEFORE TIMESTAMP(3),
   EVENT_UNIX_TIMESTAMP INT,    
   EVENT_TIMESTAMP_AS_CHAR VARCHAR(50),
   EVENT_SECOND INT) PARTITIONED BY (TICKER)
WITH ( 
   'connector' = 'kinesis',
   'stream' = 'kinesis-analytics-demo-stream',
   'aws.region' = 'us-east-1',   
   'scan.stream.initpos' = 'LATEST',
   'format' = 'json',
   'json.timestamp-format.standard' = 'ISO-8601')

Query 2 - % flink.ssql(type = 
   update
) 
      SELECT
         TICKER,
         EVENT_TIME,
         EVENT_TIME - INTERVAL '5' MINUTE AS FIVE_MINUTES_BEFORE,
         UNIX_TIMESTAMP() AS EVENT_UNIX_TIMESTAMP,
         DATE_FORMAT(EVENT_TIME, 'yyyy-MM-dd hh:mm:ss') AS EVENT_TIMESTAMP_AS_CHAR,
         EXTRACT(SECOND 
      FROM
         EVENT_TIME) AS EVENT_SECOND 
      FROM
         DESTINATION_SQL_STREAM;
```

------

### Windows and aggregation<a name="windows-aggregation"></a>

------
#### [ SQL\-based Kinesis Data Analytics application  ]

```
CREATE 
OR REPLACE STREAM "DESTINATION_SQL_STREAM" (
   event_time TIMESTAMP,
   ticker_symbol VARCHAR(4),
   ticker_count INTEGER);
CREATE 
OR REPLACE PUMP "STREAM_PUMP" AS 
INSERT INTO
   "DESTINATION_SQL_STREAM" 
   SELECT
      STREAM EVENT_TIME,
      TICKER,
      COUNT(TICKER) AS ticker_count 
   FROM
      "SOURCE_SQL_STREAM_001" WINDOWED BY STAGGER ( PARTITION BY 
         TICKER,
         EVENT_TIME RANGE INTERVAL '1' MINUTE);
```

------
#### [ Kinesis Data Analytics Studio  ]

```
Query 1 - % flink.ssql(type = 
update
) CREATE TABLE DESTINATION_SQL_STREAM (
   EVENT_TIME TIMESTAMP(3),
   WATERMARK FOR EVENT_TIME AS EVENT_TIME - INTERVAL '60' SECOND,    
   TICKER VARCHAR(4),
   TICKER_COUNT INT) PARTITIONED BY (TICKER) 
WITH ( 
   'connector' = 'kinesis',
   'stream' = 'kinesis-analytics-demo-stream',
   'aws.region' = 'us-east-1',   
   'scan.stream.initpos' = 'LATEST',
   'format' = 'json'

Query 2 - % flink.ssql(type = 
   update
) 
      SELECT
         EVENT_TIME,
         TICKER, COUNT(TICKER) AS ticker_count 
      FROM
         DESTINATION_SQL_STREAM 
      GROUP BY
         TUMBLE(EVENT_TIME,
         INTERVAL '60' second),
         EVENT_TIME, TICKER;
```

------

### Tumbling Window using Rowtime<a name="tumbling-windows-rowtime"></a>

------
#### [ SQL\-based Kinesis Data Analytics application  ]

```
CREATE 
OR REPLACE STREAM "DESTINATION_SQL_STREAM"(
   TICKER VARCHAR(4),
   MIN_PRICE REAL,
   MAX_PRICE REAL);
CREATE 
OR REPLACE PUMP "STREAM_PUMP" AS 
INSERT INTO
   "DESTINATION_SQL_STREAM"
   SELECT
      STREAM TICKER,
      MIN(PRICE),
      MAX(PRICE)
   FROM
      "SOURCE_SQL_STREAM_001"
   GROUP BY
      TICKER,
      STEP("SOURCE_SQL_STREAM_001".
            ROWTIME BY INTERVAL '60' SECOND);
```

------
#### [ Kinesis Data Analytics Studio  ]

```
Query 1 - % flink.ssql(type = 
update
) CREATE TABLE DESTINATION_SQL_STREAM (
   ticker VARCHAR(4),
   price DOUBLE,
   event_time VARCHAR(32),
   processing_time AS PROCTIME()) 
PARTITIONED BY (ticker) WITH ( 
   'connector' = 'kinesis',
   'stream' = 'kinesis-analytics-demo-stream',
   'aws.region' = 'us-east-1',
   'scan.stream.initpos' = 'LATEST',
   'format' = 'json',
   'json.timestamp-format.standard' = 'ISO-8601') 

Query 2 - % flink.ssql(type = 
   update
) 
      SELECT
         ticker,
         min(price) AS MIN_PRICE,
         max(price) AS MAX_PRICE 
      FROM
         DESTINATION_SQL_STREAM 
      GROUP BY
         TUMBLE(processing_time, INTERVAL '60' second),
         ticker;
```

------

### Retrieving the most frequently occuring values \(TOP\_K\_ITEMS\_TUMBLING\)<a name="retrieve-values"></a>

------
#### [ SQL\-based Kinesis Data Analytics application  ]

```
CREATE 
OR REPLACE STREAM "CALC_COUNT_SQL_STREAM"(TICKER VARCHAR(4),
   TRADETIME TIMESTAMP,
   TICKERCOUNT DOUBLE);
CREATE 
OR REPLACE STREAM "DESTINATION_SQL_STREAM"(
   TICKER VARCHAR(4),
   TRADETIME TIMESTAMP,
   TICKERCOUNT DOUBLE);
CREATE PUMP "CALC_COUNT_SQL_PUMP_001" AS INSERT INTO "CALC_COUNT_SQL_STREAM" (
   "TICKER",
   "TRADETIME",
   "TICKERCOUNT")
SELECT
   STREAM"TICKER_SYMBOL",
   STEP("SOURCE_SQL_STREAM_001"."ROWTIME" BY INTERVAL '1' MINUTE) as "TradeTime",
   COUNT(*) AS "TickerCount"
FROM
   "SOURCE_SQL_STREAM_001"
GROUP BY STEP("SOURCE_SQL_STREAM_001".
   ROWTIME BY INTERVAL '1' MINUTE),
   STEP("SOURCE_SQL_STREAM_001".
      "APPROXIMATE_ARRIVAL_TIME" BY INTERVAL '1' MINUTE),
   TICKER_SYMBOL;
CREATE PUMP "AGGREGATED_SQL_PUMP" AS INSERT INTO "DESTINATION_SQL_STREAM" (
   "TICKER",
   "TRADETIME",
   "TICKERCOUNT")
SELECT
   STREAM "TICKER",
   "TRADETIME",
   SUM("TICKERCOUNT") OVER W1 AS "TICKERCOUNT"
FROM
   "CALC_COUNT_SQL_STREAM" WINDOW W1 AS 
   (
      PARTITION BY "TRADETIME" RANGE INTERVAL '10' MINUTE PRECEDING
   )
;
```

------
#### [ Kinesis Data Analytics Studio  ]

```
Query 1 - % flink.ssql(type = 
update
) DROP TABLE IF EXISTS DESTINATION_SQL_STREAM;
CREATE TABLE DESTINATION_SQL_STREAM ( 
   TICKER VARCHAR(4),
   EVENT_TIME TIMESTAMP(3),
   WATERMARK FOR EVENT_TIME AS EVENT_TIME - INTERVAL '1' SECONDS ) 
PARTITIONED BY (TICKER) WITH (
   'connector' = 'kinesis', 'stream' = 'kinesis-analytics-demo-stream',
   'aws.region' = 'us-east-1',
   'scan.stream.initpos' = 'LATEST',
   'format' = 'json',
   'json.timestamp-format.standard' = 'ISO-8601');

Query 2 - % flink.ssql(type = 
update
) 
   SELECT
      * 
   FROM
      (
         SELECT
            TICKER,
            COUNT(*) as MOST_FREQUENT_VALUES,
            ROW_NUMBER() OVER (PARTITION BY TICKER 
         ORDER BY
            TICKER DESC) AS row_num 
         FROM
            DESTINATION_SQL_STREAM 
         GROUP BY
            TUMBLE(EVENT_TIME, INTERVAL '1' MINUTE),
            TICKER
      )
   WHERE
      row_num <= 5;
```

------

### Approximate Top\-K items<a name="errors"></a>

------
#### [ SQL\-based Kinesis Data Analytics application  ]

```
CREATE 
OR REPLACE STREAM "DESTINATION_SQL_STREAM" (ITEM VARCHAR(1024), ITEM_COUNT DOUBLE);
CREATE 
OR REPLACE PUMP "STREAM_PUMP" AS 
INSERT INTO
   "DESTINATION_SQL_STREAM" 
   SELECT
      STREAM ITEM,
      ITEM_COUNT 
   FROM
      TABLE(TOP_K_ITEMS_TUMBLING(CURSOR(
      SELECT
         STREAM * 
      FROM
         "SOURCE_SQL_STREAM_001"), 'column1', -- name of column in single quotes10, -- number of top items60 -- tumbling window size in seconds));
```

------
#### [ Kinesis Data Analytics Studio  ]

```
%flinkssql
DROP TABLE IF EXISTS SOURCE_SQL_STREAM_001 
CREATE TABLE SOURCE_SQL_STREAM_001 ( TS TIMESTAMP(3), WATERMARK FOR TS as TS - INTERVAL '5' SECOND, ITEM VARCHAR(1024), 
PRICE DOUBLE) 
   WITH ( 'connector' = 'kinesis', 'stream' = 'SOURCE_SQL_STREAM_001',
'aws.region' = 'us-east-1', 'scan.stream.initpos' = 'LATEST', 'format' = 'json',
'json.timestamp-format.standard' = 'ISO-8601');


%flink.ssql(type=update)
SELECT
   * 
FROM
   (
      SELECT
         *,
         ROW_NUMBER() OVER (PARTITION BY AGG_WINDOW 
      ORDER BY
         ITEM_COUNT DESC) as rownum 
      FROM
         (
            select
               AGG_WINDOW,
               ITEM,
               ITEM_COUNT 
            from
               (
                  select
                     TUMBLE_ROWTIME(TS, INTERVAL '60' SECONDS) as AGG_WINDOW,
                     ITEM,
                     count(*) as ITEM_COUNT 
                  FROM
                     SOURCE_SQL_STREAM_001 
                  GROUP BY
                     TUMBLE(TS, INTERVAL '60' SECONDS),
                     ITEM
               )
         )
   )
where
   rownum <= 3
```

------

### Parsing Web Logs \(W3C\_LOG\_PARSE Function\)<a name="errors"></a>

------
#### [ SQL\-based Kinesis Data Analytics application  ]

```
CREATE 
OR REPLACE STREAM "DESTINATION_SQL_STREAM" ( column1 VARCHAR(16), 
   column2 VARCHAR(16), 
   column3 VARCHAR(16), 
   column4 VARCHAR(16), 
   column5 VARCHAR(16), 
   column6 VARCHAR(16), 
   column7 VARCHAR(16));
CREATE 
OR REPLACE PUMP "myPUMP" ASINSERT INTO "DESTINATION_SQL_STREAM"
SELECT
   STREAM l.r.COLUMN1,
   l.r.COLUMN2,
   l.r.COLUMN3,
   l.r.COLUMN4,
   l.r.COLUMN5,
   l.r.COLUMN6,
   l.r.COLUMN7 
FROM
   (
      SELECT
         STREAM W3C_LOG_PARSE("log", 'COMMON') 
      FROM
         "SOURCE_SQL_STREAM_001"
   )
   AS l(r);
```

------
#### [ Kinesis Data Analytics Studio  ]

```
%flink.ssql(type=update)
DROP TABLE IF EXISTS SOURCE_SQL_STREAM_001 CREATE TABLE SOURCE_SQL_STREAM_001 ( log VARCHAR(1024)) 
   WITH ( 'connector' = 'kinesis', 
          'stream' = 'SOURCE_SQL_STREAM_001',
          'aws.region' = 'us-east-1',
          'scan.stream.initpos' = 'LATEST',
          'format' = 'json',
          'json.timestamp-format.standard' = 'ISO-8601');
          
% flink.ssql(type=update) 
   select
      SPLIT_INDEX(log, ' ', 0),
      SPLIT_INDEX(log, ' ', 1),
      SPLIT_INDEX(log, ' ', 2),
      SPLIT_INDEX(log, ' ', 3),
      SPLIT_INDEX(log, ' ', 4),
      SPLIT_INDEX(log, ' ', 5),
      SPLIT_INDEX(log, ' ', 6) 
   from
      SOURCE_SQL_STREAM_001;
```

------

### Split Strings into Multiple Fields \(VARIABLE\_COLUMN\_LOG\_PARSE Function\)<a name="errors"></a>

------
#### [ SQL\-based Kinesis Data Analytics application  ]

```
CREATE 
OR REPLACE STREAM "DESTINATION_SQL_STREAM"( "column_A" VARCHAR(16),
   "column_B" VARCHAR(16),
   "column_C" VARCHAR(16),
   "COL_1" VARCHAR(16),
   "COL_2" VARCHAR(16),
   "COL_3" VARCHAR(16));
CREATE 
OR REPLACE PUMP "SECOND_STREAM_PUMP" AS INSERT INTO "DESTINATION_SQL_STREAM"
SELECT
   STREAM t."Col_A",
   t."Col_B",
   t."Col_C",
   t.r."COL_1",
   t.r."COL_2",
   t.r."COL_3"
FROM
   (
      SELECT
         STREAM "Col_A",
         "Col_B",
         "Col_C",
         VARIABLE_COLUMN_LOG_PARSE ("Col_E_Unstructured",
         'COL_1 TYPE VARCHAR(16),
         COL_2 TYPE VARCHAR(16),
         COL_3 TYPE VARCHAR(16)', ',') AS r 
      FROM
         "SOURCE_SQL_STREAM_001"
   )
   as t;
```

------
#### [ Kinesis Data Analytics Studio  ]

```
%flink.ssql(type=update)
DROP TABLE IF EXISTS SOURCE_SQL_STREAM_001 CREATE TABLE SOURCE_SQL_STREAM_001 ( log VARCHAR(1024)) 
   WITH ( 'connector' = 'kinesis',
          'stream' = 'SOURCE_SQL_STREAM_001',
          'aws.region' = 'us-east-1',
          'scan.stream.initpos' = 'LATEST',
          'format' = 'json',
          'json.timestamp-format.standard' = 'ISO-8601');
          
% flink.ssql(type=update) 
   select
      SPLIT_INDEX(log, ' ', 0),
      SPLIT_INDEX(log, ' ', 1),
      SPLIT_INDEX(log, ' ', 2),
      SPLIT_INDEX(log, ' ', 3),
      SPLIT_INDEX(log, ' ', 4),
      SPLIT_INDEX(log, ' ', 5)
)
from
   SOURCE_SQL_STREAM_001;
```

------

### Joins<a name="joins"></a>

------
#### [ SQL\-based Kinesis Data Analytics application  ]

```
CREATE 
OR REPLACE STREAM "DESTINATION_SQL_STREAM" (
   ticker_symbol VARCHAR(4),
   "Company" varchar(20),
   sector VARCHAR(12),
   change DOUBLE,
   price DOUBLE);
CREATE 
OR REPLACE PUMP "STREAM_PUMP" AS 
INSERT INTO
   "DESTINATION_SQL_STREAM"
   SELECT
      STREAM ticker_symbol,
      "c"."Company",
      sector,
      change,
      priceFROM "SOURCE_SQL_STREAM_001" 
      LEFT JOIN
         "CompanyName" as "c"
         ON "SOURCE_SQL_STREAM_001".ticker_symbol = "c"."Ticker";
```

------
#### [ Kinesis Data Analytics Studio  ]

```
Query 1 - % flink.ssql(type = 
update
) CREATE TABLE DESTINATION_SQL_STREAM (
   TICKER_SYMBOL VARCHAR(4),
   SECTOR VARCHAR(12),
   CHANGE INT,
   PRICE DOUBLE ) 
PARTITIONED BY (TICKER_SYMBOL) WITH ( 
   'connector' = 'kinesis',
   'stream' = 'kinesis-analytics-demo-stream',
   'aws.region' = 'us-east-1',   
   'scan.stream.initpos' = 'LATEST',   
   'format' = 'json',
   'json.timestamp-format.standard' = 'ISO-8601');

Query 2 - CREATE TABLE CompanyName (
   Ticker VARCHAR(4),
   Company VARCHAR(4)) WITH ( 
      'connector' = 'filesystem',
      'path' = 's3://kda-demo-sample/TickerReference.csv',       
      'format' = 'csv' );

Query 3 - % flink.ssql(type = 
update
) 
   SELECT
      TICKER_SYMBOL,
      c.Company,
      SECTOR,
      CHANGE,
      PRICE 
   FROM
      DESTINATION_SQL_STREAM 
      LEFT JOIN
         CompanyName as c 
         ON DESTINATION_SQL_STREAM.TICKER_SYMBOL = c.Ticker;
```

------

### Errors<a name="errors"></a>

------
#### [ SQL\-based Kinesis Data Analytics application  ]

```
SELECT
   STREAM ticker_symbol,
   sector,
   change,
   (
      price / 0
   )
   as ProblemColumnFROM "SOURCE_SQL_STREAM_001"
WHERE
   sector SIMILAR TO '%TECH%';
```

------
#### [ Kinesis Data Analytics Studio  ]

```
Query 1 - % flink.ssql(type = 
update
) DROP TABLE IF EXISTS DESTINATION_SQL_STREAM;
CREATE TABLE DESTINATION_SQL_STREAM (
   TICKER_SYMBOL VARCHAR(4),
   SECTOR VARCHAR(16),
   CHANGE DOUBLE, 
   PRICE DOUBLE ) 
PARTITIONED BY (TICKER_SYMBOL) WITH (
   'connector' = 'kinesis',
   'stream' = 'kinesis-analytics-demo-stream',
   'aws.region' = 'us-east-1',   
   'scan.stream.initpos' = 'LATEST',
   'format' = 'json',
   'json.timestamp-format.standard' = 'ISO-8601');

Query 2 - % flink.pyflink @udf(input_types = [DataTypes.BIGINT()],
   result_type = DataTypes.BIGINT()) def DivideByZero(price): try: price / 0 
except
: return - 1 st_env.register_function("DivideByZero",
   DivideByZero) 
   
   Query 3 - % flink.ssql(type = 
update
) 
   SELECT
      CURRENT_TIMESTAMP AS ERROR_TIME,
      * 
   FROM
      (
         SELECT
            TICKER_SYMBOL,
            SECTOR,
            CHANGE,
            DivideByZero(PRICE) as ErrorColumn 
         FROM
            DESTINATION_SQL_STREAM 
         WHERE
            SECTOR SIMILAR TO '%TECH%' 
      )
      AS ERROR_STREAM;
```

------