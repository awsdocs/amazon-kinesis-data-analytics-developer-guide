# Application Code<a name="how-it-works-app-code"></a>

Application code is a series of SQL statements that process input and produce output\. These SQL statements operate on in\-application streams and reference tables\. For more information, see [Amazon Kinesis Data Analytics for SQL Applications: How It Works](how-it-works.md)\. 

For information about the SQL language elements that are supported by Kinesis Data Analytics, see [Amazon Kinesis Data Analytics SQL Reference](https://docs.aws.amazon.com/kinesisanalytics/latest/sqlref/analytics-sql-reference.html)\.

In relational databases, you work with tables, using INSERT statements to add records and the SELECT statement to query the data\. In Amazon Kinesis Data Analytics, you work with streams\. You can write a SQL statement to query these streams\. The results of querying one in\-application stream are always sent to another in\-application stream\. When performing complex analytics, you might create several in\-application streams to hold the results of intermediate analytics\. And then finally, you configure application output to persist results of the final analytics \(from one or more in\-application streams\) to external destinations\. In summary, the following is a typical pattern for writing application code:
+ The SELECT statement is always used in the context of an INSERT statement\. That is, when you select rows, you insert results into another in\-application stream\.
+ The INSERT statement is always used in the context of a pump\. That is, you use pumps to write to an in\-application stream\.

The following example application code reads records from one in\-application \(SOURCE\_SQL\_STREAM\_001\) stream and write it to another in\-application stream \(DESTINATION\_SQL\_STREAM\)\. You can insert records to in\-application streams using pumps, as shown following:

```
CREATE OR REPLACE STREAM "DESTINATION_SQL_STREAM" (ticker_symbol VARCHAR(4), 
                                                   change DOUBLE, 
                                                   price DOUBLE);
-- Create a pump and insert into output stream.
CREATE OR REPLACE PUMP "STREAM_PUMP" AS 

  INSERT INTO "DESTINATION_SQL_STREAM" 
    SELECT STREAM ticker_symbol, change,price
    FROM   "SOURCE_SQL_STREAM_001";
```

**Note**  
The identifiers that you specify for stream names and column names follow standard SQL conventions\. For example, if you put quotation marks around an identifier, it makes the identifier case sensitive\. If you don't, the identifier defaults to uppercase\. For more information about identifiers, see [Identifiers](https://docs.aws.amazon.com/kinesisanalytics/latest/sqlref/sql-reference-identifiers.html) in the *Amazon Kinesis Data Analytics SQL Reference*\.

Your application code can consist of many SQL statements\. For example:
+ You can write SQL queries in a sequential manner where the result of one SQL statement feeds into the next SQL statement\.
+ You can also write SQL queries that run independent of each other\. For example, you can write two SQL statements that query the same in\-application stream, but send output into different in\-applications streams\. You can then query the newly created in\-application streams independently\. 

You can create in\-application streams to save intermediate results\. You insert data in in\-application streams using pumps\. For more information, see [In\-Application Streams and Pumps](streams-pumps.md)\.

If you add an in\-application reference table, you can write SQL to join data in in\-application streams and reference tables\. For more information, see [Example: Adding Reference Data to a Kinesis Data Analytics Application](app-add-reference-data.md)\.

According to the application's output configuration, Amazon Kinesis Data Analytics writes data from specific in\-application streams to the external destination according to the application's output configuration\. Make sure that your application code writes to the in\-application streams specified in the output configuration\. 

For more information, see the following topics:
+  [Streaming SQL Concepts](streaming-sql-concepts.md)
+ [Amazon Kinesis Data Analytics SQL Reference](https://docs.aws.amazon.com/kinesisanalytics/latest/sqlref/analytics-sql-reference.html)