# Example: Simple Alerts<a name="app-simple-alerts"></a>

In this application, the query runs continuously on the in\-application stream created over the demo stream\. For more information, see [Continuous Queries](continuous-queries-concepts.md)\. If any rows show a stock price change that is greater than 1 percent, those rows are inserted in another in\-application stream\. In the exercise, you can configure the application output persist the results to an external destination\. You can then further investigate results\. For example, you can use an AWS Lambda function to process records and send you alerts\. 

**To create a simple alerts application**

1. Create the Amazon Kinesis Data Analytics application as described in the [Getting Started Exercise](http://docs.aws.amazon.com/kinesisanalytics/latest/dev/get-started-exercise.html)\.

1. In the SQL editor, replace the application code with the following: 

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

   The SELECT statement in the application code filters rows in the `SOURCE_SQL_STREAM_001` for stock price changes greater than 1%, and inserts those rows to another in\-application stream `DESTINATION_SQL_STREAM` using a pump\. For more information about the coding pattern that explains using pumps to insert rows in in\-application streams, see [Application Code](how-it-works-app-code.md)\.

1. Click **Save and run SQL**\.

1. Add a destination\. You can either choose the **Destination** in the SQL Editor, or choose **Add a destination** on the application hub\.

   1. In SQL editor, choose the **Destination** tab and then choose **Add a destination**\. 

      On the **Add a destination **page, choose **Configure a new stream**\.   
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/kinesisanalytics/latest/dev/images/alerts-10.png)

   1. Choose **Go to Kinesis Streams**\. 

   1. In the Amazon Kinesis Data Streams console, create a new Kinesis stream \(for example, `gs-destination`\) with 1 shard\. Wait until the stream status is **ACTIVE**\.

   1. Return to the Amazon Kinesis Data Analytics console\. On the **Destination** page, choose the stream that you created\. 

      If the stream does not show, refresh the page\.

      Now you have an external destination, where Amazon Kinesis Data Analytics persists any records your application writes to the in\-application stream `DESTINATION_SQL_STREAM`\.

   1. Choose **Save and continue**\.

   Now you have an external destination, a Kinesis stream, where Amazon Kinesis Data Analytics persists your application output in the `DESTINATION_SQL_STREAM` in\-application stream\.

1. Configure AWS Lambda to monitor the Kinesis stream you created and invoke a Lambda function\. 

   For instructions, see [Preprocessing Data Using a Lambda Function](lambda-preprocessing.md)\.