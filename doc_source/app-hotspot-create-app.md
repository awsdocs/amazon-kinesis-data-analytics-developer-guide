# Step 2: Create the Kinesis Data Analytics Application<a name="app-hotspot-create-app"></a>

In this section of the [Hotspots example](app-hotspots-detection.md), you create an Amazon Kinesis data analytics application as follows:
+ Configure the application input to use the Kinesis data stream you created as the streaming source in [Step 1](app-hotspots-prepare.md)\.
+ Use the provided application code in the AWS Management Console\.

**To create an application**

1. Create a Kinesis data analytics application by following steps 1, 2, and 3 in the [Getting Started](https://docs.aws.amazon.com/kinesisanalytics/latest/dev/get-started-exercise.html) exercise \(see [Step 3\.1: Create an Application](get-started-create-app.md)\)\.

   In the source configuration, do the following:
   + Specify the streaming source you created in [Step 1: Create the Input and Output Streams](app-hotspots-prepare.md)\.
   + After the console infers the schema, edit the schema\. Ensure that the `x` and `y` column types are set to `DOUBLE` and that the `IS_HOT` column type is set to `VARCHAR`\. 

1. Use the following application code \(you can paste this code into the SQL editor\):

   ```
   CREATE OR REPLACE STREAM "DESTINATION_SQL_STREAM" (
       "x" DOUBLE, 
       "y" DOUBLE, 
       "is_hot" VARCHAR(4),
       HOTSPOTS_RESULT VARCHAR(10000)
   ); 
   CREATE OR REPLACE PUMP "STREAM_PUMP" AS 
       INSERT INTO "DESTINATION_SQL_STREAM" 
       SELECT "x", "y", "is_hot", "HOTSPOTS_RESULT" 
       FROM TABLE (
           HOTSPOTS(   
               CURSOR(SELECT STREAM "x", "y", "is_hot" FROM "SOURCE_SQL_STREAM_001"), 
               1000, 
               0.2, 
               17)
       );
   ```

1. Run the SQL code and review the results\.  
![\[Results of SQL code showing rowtime, hotspot, and hotspot_heat.\]](http://docs.aws.amazon.com/kinesisanalytics/latest/dev/images/hotspot-v2-40.png)

**Next Step**  
[Step 3: Configure the Application Output](app-hotspots-create-ka-app-config-destination.md)