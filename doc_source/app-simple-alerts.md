# Example: Creating Simple Alerts<a name="app-simple-alerts"></a>

In this Amazon Kinesis data analytics application, the query runs continuously on the in\-application stream that is created over the demo stream\. For more information, see [Continuous Queries](continuous-queries-concepts.md)\. 

If any rows show a stock price change that is greater than 1 percent, those rows are inserted into another in\-application stream\. In the exercise, you can configure the application output to persist the results to an external destination\. You can then further investigate the results\. For example, you can use an AWS Lambda function to process records and send you alerts\. 

**To create a simple alerts application**

1. Create the analytics application as described in the Kinesis Data Analytics [Getting Started](https://docs.aws.amazon.com/kinesisanalytics/latest/dev/get-started-exercise.html) exercise\.

1. In the SQL editor in Kinesis Data Analytics, replace the application code with the following: 

   ```
   CREATE OR REPLACE STREAM "DESTINATION_SQL_STREAM" 
              (ticker_symbol VARCHAR(4), 
               sector        VARCHAR(12), 
               change        DOUBLE, 
               price         DOUBLE);
   
   CREATE OR REPLACE PUMP "STREAM_PUMP" AS 
      INSERT INTO "DESTINATION_SQL_STREAM"
         SELECT STREAM ticker_symbol, sector, change, price 
         FROM   "SOURCE_SQL_STREAM_001"
         WHERE  (ABS(Change / (Price - Change)) * 100) > 1;
   ```

   The `SELECT` statement in the application code filters rows in the `SOURCE_SQL_STREAM_001` for stock price changes greater than 1 percent\. It then inserts those rows into another in\-application stream `DESTINATION_SQL_STREAM` using a pump\. For more information about the coding pattern that explains using pumps to insert rows into in\-application streams, see [Application Code](how-it-works-app-code.md)\.

1. Choose **Save and run SQL**\.

1. Add a destination\. To do this, either choose the **Destination** tab in the SQL editor or choose **Add a destination** on the application details page\.

   1. In the SQL editor, choose the **Destination** tab, and then choose **Connect to a destination**\. 

      On the **Connect to destination** page, choose **Create New**\. 

   1. Choose **Go to Kinesis Streams**\. 

   1. On the Amazon Kinesis Data Streams console, create a new Kinesis stream \(for example, `gs-destination`\) with one shard\. Wait until the stream status is **ACTIVE**\.

   1. Return to the Kinesis Data Analytics console\. On the **Connect to destination** page, choose the stream that you created\. 

      If the stream does not appear, refresh the page\.

   1. Choose **Save and continue**\.

   Now you have an external destination, a Kinesis data stream, where Kinesis Data Analytics persists your application output in the `DESTINATION_SQL_STREAM` in\-application stream\.

1. Configure AWS Lambda to monitor the Kinesis stream you created and invoke a Lambda function\. 

   For instructions, see [Preprocessing Data Using a Lambda Function](lambda-preprocessing.md)\.