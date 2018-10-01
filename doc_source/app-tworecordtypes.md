# Example: Transforming Multiple Data Types<a name="app-tworecordtypes"></a>

 A common requirement in extract, transform, and load \(ETL\) applications is to process multiple record types on a streaming source\. You can create Amazon Kinesis data analytics applications to process these kinds of streaming sources\. The process is as follows:

1. First, you map the streaming source to an in\-application input stream, similar to all other Kinesis data analytics applications\.

1. Then, in your application code, you write SQL statements to retrieve rows of specific types from the in\-application input stream\. You then insert them into separate in\-application streams\. \(You can create additional in\-application streams in your application code\.\)

In this exercise, you have a streaming source that receives records of two types \(`Order` and `Trade`\)\. These are stock orders and corresponding trades\. For each order, there can be zero or more trades\. Example records of each type are shown following:

**Order record**

```
{"RecordType": "Order", "Oprice": 9047, "Otype": "Sell", "Oid": 3811, "Oticker": "AAAA"}
```

**Trade record**

```
{"RecordType": "Trade", "Tid": 1, "Toid": 3812, "Tprice": 2089, "Tticker": "BBBB"}
```

When you create an application using the AWS Management Console, the console displays the following inferred schema for the in\-application input stream created\. By default, the console names this in\-application stream `SOURCE_SQL_STREAM_001`\.

![\[Console screenshot showing the formatted in-application stream sample.\]](http://docs.aws.amazon.com/kinesisanalytics/latest/dev/images/two-record-types-10.png)

When you save the configuration, Amazon Kinesis Data Analytics continuously reads data from the streaming source and inserts rows in the in\-application stream\. You can now perform analytics on data in the in\-application stream\. 

In the application code in this example, you first create two additional in\-application streams, `Order_Stream` and `Trade_Stream`\. You then filter the rows from the `SOURCE_SQL_STREAM_001` stream based on the record type and insert them in the newly created streams using pumps\. For information about this coding pattern, see [Application Code](how-it-works-app-code.md)\.

1. Filter order and trade rows into separate in\-application streams:

   1. Filter the order records in the `SOURCE_SQL_STREAM_001`, and save the orders in the `Order_Stream`\.

      ```
      --Create Order_Stream.
      CREATE OR REPLACE STREAM "Order_Stream" 
                 ( 
                  order_id     integer, 
                  order_type   varchar(10),
                  ticker       varchar(4),
                  order_price  DOUBLE, 
                  record_type  varchar(10)
                  );
      
      CREATE OR REPLACE PUMP "Order_Pump" AS 
         INSERT INTO "Order_Stream"
            SELECT STREAM oid, otype,oticker, oprice, recordtype 
            FROM   "SOURCE_SQL_STREAM_001"
            WHERE  recordtype = 'Order';
      ```

   1. Filter the trade records in the `SOURCE_SQL_STREAM_001`, and save the orders in the `Trade_Stream`\.

      ```
      --Create Trade_Stream.      
      CREATE OR REPLACE STREAM "Trade_Stream" 
                 (trade_id     integer, 
                  order_id     integer, 
                  trade_price  DOUBLE, 
                  ticker       varchar(4),
                  record_type  varchar(10)
                  );
      
      CREATE OR REPLACE PUMP "Trade_Pump" AS 
         INSERT INTO "Trade_Stream"
            SELECT STREAM tid, toid, tprice, tticker, recordtype
            FROM   "SOURCE_SQL_STREAM_001"
            WHERE  recordtype = 'Trade';
      ```

1. Now you can perform additional analytics on these streams\. In this example, you count the number of trades by the ticker in a one\-minute [tumbling window](https://docs.aws.amazon.com/kinesisanalytics/latest/dev/tumbling-window-concepts.html) and save the results to yet another stream, `DESTINATION_SQL_STREAM`\. 

   ```
   --do some analytics on the Trade_Stream and Order_Stream. 
   -- To see results in console you must write to OPUT_SQL_STREAM.
   
   CREATE OR REPLACE STREAM "DESTINATION_SQL_STREAM" (
               ticker  varchar(4),
               trade_count   integer
               );
   
   CREATE OR REPLACE PUMP "Output_Pump" AS 
      INSERT INTO "DESTINATION_SQL_STREAM"
         SELECT STREAM ticker, count(*) as trade_count
         FROM   "Trade_Stream"
         GROUP BY ticker,
                   FLOOR("Trade_Stream".ROWTIME TO MINUTE);
   ```

   You see the result, as shown following:  
![\[Console screenshot showing the results on the SQL results tab.\]](http://docs.aws.amazon.com/kinesisanalytics/latest/dev/images/two-record-types-20.png)

**Topics**
+ [Step 1: Prepare the Data](tworecordtypes-prepare.md)
+ [Step 2: Create the Application](tworecordtypes-create-app.md)

**Next Step**  
[Step 1: Prepare the Data](tworecordtypes-prepare.md)