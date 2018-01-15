# Step 2: Create an Application<a name="app-anom-score-create-app"></a>

In this section, you create an Amazon Kinesis Data Analytics application as follows:

+ Configure the application input to use the Kinesis stream you created in the preceding section as the streaming source\.

+ Use the **Anomaly Detection** template in the console\. 

**To create an application**

1. Follow steps 1, 2, and 3 in Getting Started exercise \(see [Step 3\.1: Create an Application](get-started-create-app.md)\) to create an application\. Note the following:

   + In the source configuration, do the following:

     + Specify the streaming source you created in the preceding section\. 

     + After the console infers the schema, edit the schema and set the `heartRate` column type to INTEGER\. 

       Most of the heart rate values are normal and the discovery process will most likely assign TINYINT type to this column\. But very small percentage of values that show high heart rate\. If these high values don't fit in the TINYINT type, Amazon Kinesis Data Analytics sends these rows to error stream\. Update the data type to INTEGER so that it can accommodate all of the generated heart rate data\.

   + Use the **Anomaly Detection** template in the console\. You then update the template code to provide appropriate column name\. 

1. Update the application code by providing column names\. The resulting application code is shown following \(you can paste this code into the SQL editor\):

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

1. Run the SQL code and review results:  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/kinesisanalytics/latest/dev/images/anom-v2-40.png)

**Next Step**  
[Step 3: Configure Application Output](app-anomaly-create-ka-app-config-destination.md)