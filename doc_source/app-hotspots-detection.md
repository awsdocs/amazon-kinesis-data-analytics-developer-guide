# Example: Detecting Hotspots on a Stream \(HOTSPOTS Function\)<a name="app-hotspots-detection"></a>

Amazon Kinesis Data Analytics provides the `HOTSPOTS` function, which can locate and return information about relatively dense regions in your data\. For more information, see [HOTSPOTS](https://docs.aws.amazon.com/kinesisanalytics/latest/sqlref/sqlrf-hotspots.html) in the *Amazon Kinesis Data Analytics SQL Reference*\. 

In this exercise, you write application code to locate hotspots on your application's streaming source\. To set up the application, you do the following steps:

1. **Set up a streaming source** – You set up a Kinesis stream and write sample coordinate data as shown following:

   ```
   {"x": 7.921782426109737, "y": 8.746265312709893, "is_hot": "N"}
   {"x": 0.722248626528026, "y": 4.648868803193405, "is_hot": "Y"}
   ```

   The example provides a Python script for you to populate the stream\. The `x` and `y` values are randomly generated, with some records being clustered around certain locations\.

   The `is_hot` field is provided as an indicator if the script intentionally generated the value as part of a hotspot\. This can help you evaluate whether the hotspot detection function is working properly\.

1. **Create the application** – Using the AWS Management Console, you then create a Kinesis data analytics application\. Configure the application input by mapping the streaming source to an in\-application stream \(`SOURCE_SQL_STREAM_001`\)\. When the application starts, Kinesis Data Analytics continuously reads the streaming source and inserts records into the in\-application stream\.

   In this exercise, you use the following code for the application:

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

   The code reads rows in the `SOURCE_SQL_STREAM_001`, analyzes it for significant hotspots, and writes the resulting data to another in\-application stream \(`DESTINATION_SQL_STREAM`\)\. You use pumps to insert rows in in\-application streams\. For more information, see [In\-Application Streams and Pumps](streams-pumps.md)\.

1. **Configure the output** – You configure the application output to send data from the application to an external destination, which is another Kinesis data stream\. Review the hotspot scores and determine what scores indicate that a hotspot occurred \(and that you need to be alerted\)\. You can use an AWS Lambda function to further process hotspot information and configure alerts\. 

1. **Verify the output** – The example includes a JavaScript application that reads data from the output stream and displays it graphically, so you can view the hotspots that the application generates in real time\. 

The exercise uses the US West \(Oregon\) \(`us-west-2`\) AWS Region to create these streams and your application\. If you use any other Region, update the code accordingly\.

**Topics**
+ [Step 1: Create the Input and Output Streams](app-hotspots-prepare.md)
+ [Step 2: Create the Kinesis Data Analytics Application](app-hotspot-create-app.md)
+ [Step 3: Configure the Application Output](app-hotspots-create-ka-app-config-destination.md)
+ [Step 4: Verify the Application Output](app-hotspots-verify-output.md)