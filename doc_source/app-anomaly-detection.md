# Example: Detecting Data Anomalies on a Stream \(RANDOM\_CUT\_FOREST Function\)<a name="app-anomaly-detection"></a>

Amazon Kinesis Data Analytics provides a function \(`RANDOM_CUT_FOREST`\) that can assign an anomaly score to each record based on values in the numeric columns\. For more information, see [`RANDOM_CUT_FOREST` Function](https://docs.aws.amazon.com/kinesisanalytics/latest/sqlref/analytics-sql-reference.html) in the *Amazon Kinesis Data Analytics SQL Reference*\. 

In this exercise, you write application code to assign an anomaly score to records on your application's streaming source\. To set up the application, you do the following:

1. **Set up a streaming source** – You set up a Kinesis data stream and write sample `heartRate` data, as shown following:

   ```
   {"heartRate": 60, "rateType":"NORMAL"}
   ...
   {"heartRate": 180, "rateType":"HIGH"}
   ```

   The procedure provides a Python script for you to populate the stream\. The `heartRate` values are randomly generated, with 99 percent of the records having `heartRate` values between 60 and 100, and only 1 percent of `heartRate` values between 150 and 200\. Thus, the records that have `heartRate` values between 150 and 200 are anomalies\. 

1. **Configure input** – Using the console, you create a Kinesis Data Analytics application and configure the application input by mapping the streaming source to an in\-application stream \(`SOURCE_SQL_STREAM_001`\)\. When the application starts, Kinesis Data Analytics continuously reads the streaming source and inserts records into the in\-application stream\.

1. **Specify application code** – The example uses the following application code:

   ```
   --Creates a temporary stream.
   CREATE OR REPLACE STREAM "TEMP_STREAM" (
   	        "heartRate"        INTEGER,
   	        "rateType"         varchar(20),
   	        "ANOMALY_SCORE"    DOUBLE);
   
   --Creates another stream for application output.	        
   CREATE OR REPLACE STREAM "DESTINATION_SQL_STREAM" (
   	        "heartRate"        INTEGER,
   	        "rateType"         varchar(20),
   	        "ANOMALY_SCORE"    DOUBLE);
   
   -- Compute an anomaly score for each record in the input stream
   -- using Random Cut Forest
   CREATE OR REPLACE PUMP "STREAM_PUMP" AS 
      INSERT INTO "TEMP_STREAM"
         SELECT STREAM "heartRate", "rateType", ANOMALY_SCORE 
         FROM TABLE(RANDOM_CUT_FOREST(
                 CURSOR(SELECT STREAM * FROM "SOURCE_SQL_STREAM_001")));
   
   -- Sort records by descending anomaly score, insert into output stream
   CREATE OR REPLACE PUMP "OUTPUT_PUMP" AS 
      INSERT INTO "DESTINATION_SQL_STREAM"
         SELECT STREAM * FROM "TEMP_STREAM"
         ORDER BY FLOOR("TEMP_STREAM".ROWTIME TO SECOND), ANOMALY_SCORE DESC;
   ```

   The code reads rows in the `SOURCE_SQL_STREAM_001`, assigns an anomaly score, and writes the resulting rows to another in\-application stream \(`TEMP_STREAM`\)\. The application code then sorts the records in the `TEMP_STREAM` and saves the results to another in\-application stream \(`DESTINATION_SQL_STREAM`\)\. You use pumps to insert rows in in\-application streams\. For more information, see [In\-Application Streams and Pumps](streams-pumps.md)\.

1. **Configure output** – You configure the application output to persist data in the `DESTINATION_SQL_STREAM` to an external destination, which is another Kinesis data stream\. Reviewing the anomaly scores that are assigned to each record and determining what score indicates an anomaly \(and that you need to be alerted\) is external to the application\. You can use an AWS Lambda function to process these anomaly scores and configure alerts\. 

The exercise uses the US East \(N\. Virginia\) \(`us-east-1`\) AWS Region to create these streams and your application\. If you use any other Region, you must update the code accordingly\.

**Topics**
+ [Step 1: Prepare](app-anomaly-prepare.md)
+ [Step 2: Create an Application](app-anom-score-create-app.md)
+ [Step 3: Configure Application Output](app-anomaly-create-ka-app-config-destination.md)
+ [Step 4: Verify Output](app-anomaly-verify-output.md)

**Next Step**  
[Step 1: Prepare](app-anomaly-prepare.md)