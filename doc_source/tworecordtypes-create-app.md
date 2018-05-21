# Step 2: Create the Application<a name="tworecordtypes-create-app"></a>

In this section, you create an Amazon Kinesis data analytics application\. You then update the application by adding input configuration that maps the streaming source you created in the preceding section to an in\-application input stream\. 

1. Open the Kinesis Data Analytics console at [ https://console\.aws\.amazon\.com/kinesisanalytics](https://console.aws.amazon.com/kinesisanalytics)\.

1. Choose **Create application**\. This example uses the application name **ProcessMultipleRecordTypes**\.

1. On the application details page, choose **Connect streaming data** to connect to the source\. 

1. On the **Connect to source** page, do the following:

   1. Choose the stream that you created in [Step 1: Prepare the Data](tworecordtypes-prepare.md)\. 

   1. Choose to create an IAM role\.

   1. Wait for the console to show the inferred schema and samples records that are used to infer the schema for the in\-application stream created\.

   1. Choose **Save and continue**\.

1. On the application hub, choose **Go to SQL editor**\. To start the application, choose **Yes, start application** in the dialog box that appears\.

1. In the SQL editor, write the application code and verify the results:

   1. Copy the following application code and paste it into the editor\.

      ```
      --Create Order_Stream.
      CREATE OR REPLACE STREAM "Order_Stream" 
                 ( 
                  "order_id"     integer, 
                  "order_type"   varchar(10),
                  "ticker"       varchar(4),
                  "order_price"  DOUBLE, 
                  "record_type"  varchar(10)
                  );
      
      CREATE OR REPLACE PUMP "Order_Pump" AS 
         INSERT INTO "Order_Stream"
            SELECT STREAM "Oid", "Otype","Oticker", "Oprice", "RecordType" 
            FROM   "SOURCE_SQL_STREAM_001"
            WHERE  "RecordType" = 'Order';
      --********************************************
      --Create Trade_Stream.      
      CREATE OR REPLACE STREAM "Trade_Stream" 
                 ("trade_id"     integer, 
                  "order_id"     integer, 
                  "trade_price"  DOUBLE, 
                  "ticker"       varchar(4),
                  "record_type"  varchar(10)
                  );
      
      CREATE OR REPLACE PUMP "Trade_Pump" AS 
         INSERT INTO "Trade_Stream"
            SELECT STREAM "Tid", "Toid", "Tprice", "Tticker", "RecordType"
            FROM   "SOURCE_SQL_STREAM_001"
            WHERE  "RecordType" = 'Trade';
      --*****************************************************************
      --do some analytics on the Trade_Stream and Order_Stream. 
      CREATE OR REPLACE STREAM "DESTINATION_SQL_STREAM" (
                  "ticker"  varchar(4),
                  "trade_count"   integer
                  );
      
      CREATE OR REPLACE PUMP "Output_Pump" AS 
         INSERT INTO "DESTINATION_SQL_STREAM"
            SELECT STREAM "ticker", count(*) as trade_count
            FROM   "Trade_Stream"
            GROUP BY "ticker",
                      FLOOR("Trade_Stream".ROWTIME TO MINUTE);
      ```

   1. Choose **Save and run SQL**\. Choose the **Real\-time analytics** tab to see all of the in\-application streams that the application created and verify the data\. 

**Next Step**  
You can configure application output to persist results to an external destination, such as another Kinesis stream or a Kinesis Data Firehose data delivery stream\. 