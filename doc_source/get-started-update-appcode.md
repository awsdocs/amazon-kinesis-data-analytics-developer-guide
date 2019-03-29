# Step 3\.4: \(Optional\) Update the Application Code<a name="get-started-update-appcode"></a>

In this step, you explore how to update the application code\. 

**To update application code**

1. Create another in\-application stream as follows:
   + Create another in\-application stream called `DESTINATION_SQL_STREAM_2`\.
   + Create a pump, and then use it to insert rows in the newly created stream by selecting rows from the `DESTINATION_SQL_STREAM`\.

   In the SQL editor, append the following code to the existing application code:

   ```
   CREATE OR REPLACE STREAM "DESTINATION_SQL_STREAM_2" 
              (ticker_symbol VARCHAR(4), 
               change        DOUBLE, 
               price         DOUBLE);
   
   CREATE OR REPLACE PUMP "STREAM_PUMP_2" AS 
      INSERT INTO "DESTINATION_SQL_STREAM_2"
         SELECT STREAM ticker_symbol, change, price 
         FROM   "DESTINATION_SQL_STREAM";
   ```

   Save and run the code\. Additional in\-application streams appear on the **Real\-time analytics** tab\.

1. Create two in\-application streams\. Filter rows in the `SOURCE_SQL_STREAM_001` based on the stock ticker, and then insert them in to these separate streams\. 

   Append the following SQL statements to your application code:

   ```
   CREATE OR REPLACE STREAM "AMZN_STREAM" 
              (ticker_symbol VARCHAR(4), 
               change        DOUBLE, 
               price         DOUBLE);
   
   CREATE OR REPLACE PUMP "AMZN_PUMP" AS 
      INSERT INTO "AMZN_STREAM"
         SELECT STREAM ticker_symbol, change, price 
         FROM   "SOURCE_SQL_STREAM_001"
         WHERE  ticker_symbol SIMILAR TO '%AMZN%';
   
   CREATE OR REPLACE STREAM "TGT_STREAM" 
              (ticker_symbol VARCHAR(4), 
               change        DOUBLE, 
               price         DOUBLE);
   
   CREATE OR REPLACE PUMP "TGT_PUMP" AS 
      INSERT INTO "TGT_STREAM"
         SELECT STREAM ticker_symbol, change, price 
         FROM   "SOURCE_SQL_STREAM_001"
         WHERE  ticker_symbol SIMILAR TO '%TGT%';
   ```

   Save and run the code\. Notice additional in\-application streams on the **Real\-time analytics** tab\.

You now have your first working Amazon Kinesis Data Analytics application\. In this exercise, you did the following: 
+ Created your first Kinesis data analytics application\.

   
+ Configured application input that identified the demo stream as the streaming source and mapped it to an in\-application stream \(`SOURCE_SQL_STREAM_001`\) that is created\. Kinesis Data Analytics continuously reads the demo stream and inserts records in the in\-application stream\.

   
+ Your application code queried the `SOURCE_SQL_STREAM_001` and wrote output to another in\-application stream called `DESTINATION_SQL_STREAM`\. 

Now you can optionally configure application output to write the application output to an external destination\. That is, you can configure the application output to write records in the `DESTINATION_SQL_STREAM` to an external destination\. For this exercise, this is an optional step\. To learn how to configure the destination, go to the next step\.

**Next Step**  
[Step 4 \(Optional\) Edit the Schema and SQL Code Using the Console](console-feature-summary.md)\.