# Step 2: Create an Application<a name="app-anom-score-create-app"></a>

In this section, you create an Amazon Kinesis Data Analytics application as follows:
+ Configure the application input to use the Kinesis data stream that you created in [Step 1: Prepare](app-anomaly-prepare.md) as the streaming source\.
+ Use the **Anomaly Detection** template on the console\. 

**To create an application**

1. Follow steps 1, 2, and 3 in the Kinesis Data Analytics **Getting Started** exercise \(see [Step 3\.1: Create an Application](get-started-create-app.md)\)\. 
   + In the source configuration, do the following:
     + Specify the streaming source that you created in the preceding section\. 
     + After the console infers the schema, edit the schema, and set the `heartRate` column type to `INTEGER`\. 

       Most of the heart rate values are normal, and the discovery process will most likely assign the `TINYINT` type to this column\. But a small percentage of the values show a high heart rate\. If these high values don't fit in the `TINYINT` type, Kinesis Data Analytics sends these rows to an error stream\. Update the data type to `INTEGER` so that it can accommodate all the generated heart rate data\.
   + Use the **Anomaly Detection** template on the console\. You then update the template code to provide the appropriate column name\.

1. Update the application code by providing column names\. The resulting application code is shown following \(paste this code into the SQL editor\):

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

1. Run the SQL code and review the results on the Kinesis Data Analytics console:  
![\[Console screenshot showing real-time analytics tab with the resulting data in the in-application stream.\]](http://docs.aws.amazon.com/kinesisanalytics/latest/dev/images/anom-v2-40.png)

**Next Step**  
[Step 3: Configure Application Output](app-anomaly-create-ka-app-config-destination.md)