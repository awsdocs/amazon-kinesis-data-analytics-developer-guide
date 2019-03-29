# Step 2: Create an Analytics Application<a name="app-anom-with-exp-create-app"></a>

In this section, you create an Amazon Kinesis Data Analytics application and configure it to use the Kinesis data stream that you created as the streaming source in [Step 1: Prepare the Data](app-anomaly-with-ex-prepare.md)\. You then run application code that uses the `RANDOM_CUT_FOREST_WITH_EXPLANATION` function\.

**To create an application**

1. Open the Kinesis console at [https://console\.aws\.amazon\.com/kinesis](https://console.aws.amazon.com/kinesis)\.

1. Choose **Data Analytics** in the navigation pane, and then choose **Create application**\.

1. Provide an application name and description \(optional\), and choose **Create application**\.

1. Choose **Connect streaming data**, and then choose **ExampleInputStream** from the list\. 

1. Choose **Discover schema**, and make sure that `Systolic` and `Diastolic` appear as `INTEGER` columns\. If they have another type, choose **Edit schema**, and assign the type `INTEGER` to both of them\. 

1. Under **Real time analytics**, choose **Go to SQL editor**\. When prompted, choose to run your application\. 

1. Paste the following code into the SQL editor, and then choose **Save and run SQL**\.

   ```
   --Creates a temporary stream.
   CREATE OR REPLACE STREAM "TEMP_STREAM" (
   	        "Systolic"                  INTEGER,
   	        "Diastolic"                 INTEGER,
   	        "BloodPressureLevel"        varchar(20),
   	        "ANOMALY_SCORE"             DOUBLE,
   	        "ANOMALY_EXPLANATION"       varchar(512));
   
   --Creates another stream for application output.	        
   CREATE OR REPLACE STREAM "DESTINATION_SQL_STREAM" (
   	        "Systolic"                  INTEGER,
   	        "Diastolic"                 INTEGER,
   	        "BloodPressureLevel"        varchar(20),
   	        "ANOMALY_SCORE"             DOUBLE,
   	        "ANOMALY_EXPLANATION"       varchar(512));
   
   -- Compute an anomaly score with explanation for each record in the input stream
   -- using RANDOM_CUT_FOREST_WITH_EXPLANATION
   CREATE OR REPLACE PUMP "STREAM_PUMP" AS 
      INSERT INTO "TEMP_STREAM"
         SELECT STREAM "Systolic", "Diastolic", "BloodPressureLevel", ANOMALY_SCORE, ANOMALY_EXPLANATION 
         FROM TABLE(RANDOM_CUT_FOREST_WITH_EXPLANATION(
                 CURSOR(SELECT STREAM * FROM "SOURCE_SQL_STREAM_001"), 100, 256, 100000, 1, true));
   
   -- Sort records by descending anomaly score, insert into output stream
   CREATE OR REPLACE PUMP "OUTPUT_PUMP" AS 
      INSERT INTO "DESTINATION_SQL_STREAM"
         SELECT STREAM * FROM "TEMP_STREAM"
         ORDER BY FLOOR("TEMP_STREAM".ROWTIME TO SECOND), ANOMALY_SCORE DESC;
   ```

**Next Step**  
[Step 3: Examine the Results](examine-results-with-exp.md)