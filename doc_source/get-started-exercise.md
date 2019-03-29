# Step 3: Create Your Starter Amazon Kinesis Data Analytics Application<a name="get-started-exercise"></a>

By following the steps in this section, you can create your first Kinesis Data Analytics application using the console\. 

**Note**  
We suggest that you review [Amazon Kinesis Data Analytics for SQL Applications: How It Works](how-it-works.md) before trying the Getting Started exercise\.

For this Getting Started exercise, you can use the console to work with either the demo stream or templates with application code\.
+ If you choose to use the demo stream, the console creates a Kinesis data stream in your account that is called `kinesis-analytics-demo-stream`\.

  A Kinesis data analytics application requires a streaming source\. For this source, several SQL examples in this guide use the demo stream `kinesis-analytics-demo-stream`\. The console also runs a script that continuously adds sample data \(simulated stock trade records\) to this stream, as shown following\.  
![\[Formatted stream sample table showing stock symbols, sectors, and prices.\]](http://docs.aws.amazon.com/kinesisanalytics/latest/dev/images/gs-v2-30.png)

  You can use `kinesis-analytics-demo-stream` as the streaming source for your application in this exercise\.
**Note**  
The demo stream remains in your account\. You can use it to test other examples in this guide\. However, when you leave the console, the script that the console uses stops populating the data\. When needed, the console provides the option to start populating the stream again\. 
+ If you choose to use the templates with example application code, you use template code that the console provides to perform simple analytics on the demo stream\. 

You use these features to quickly set up your first application as follows:

1. **Create an application** – You only need to provide a name\. The console creates the application and the service sets the application state to `READY`\.

    

1. **Configure input** – First, you add a streaming source, the demo stream\. You must create a demo stream in the console before you can use it\. Then, the console takes a random sample of records on the demo stream and infers a schema for the in\-application input stream that is created\. The console names the in\-application stream `SOURCE_SQL_STREAM_001`\.

   The console uses the discovery API to infer the schema\. If necessary, you can edit the inferred schema\. For more information, see [DiscoverInputSchema](API_DiscoverInputSchema.md)\. Kinesis Data Analytics uses this schema to create an in\-application stream\.

    

   When you start the application, Kinesis Data Analytics reads the demo stream continuously on your behalf and inserts rows in the `SOURCE_SQL_STREAM_001` in\-application input stream\. 

    

1. **Specify application code** – You use a template \(called **Continuous filter**\) that provides the following code:

   ```
   CREATE OR REPLACE STREAM "DESTINATION_SQL_STREAM" 
     (symbol VARCHAR(4), sector VARCHAR(12), CHANGE DOUBLE, price DOUBLE);
    
   -- Create pump to insert into output. 
   CREATE OR REPLACE PUMP "STREAM_PUMP" AS 
      INSERT INTO "DESTINATION_SQL_STREAM"  
         SELECT STREAM ticker_symbol, sector, CHANGE, price
         FROM "SOURCE_SQL_STREAM_001"
         WHERE sector SIMILAR TO '%TECH%';
   ```

   The application code queries the in\-application stream `SOURCE_SQL_STREAM_001`\. The code then inserts the resulting rows in another in\-application stream `DESTINATION_SQL_STREAM`, using pumps\. For more information about this coding pattern, see [Application Code](how-it-works-app-code.md)\. 

   For information about the SQL language elements that are supported by Kinesis Data Analytics, see [Amazon Kinesis Data Analytics SQL Reference](https://docs.aws.amazon.com/kinesisanalytics/latest/sqlref/analytics-sql-reference.html)\.

    

1. **Configuring output** – In this exercise, you don't configure any output\. That is, you don't persist data in the in\-application stream that your application creates to any external destination\. Instead, you verify query results in the console\. Additional examples in this guide show how to configure output\. For one example, see [Example: Creating Simple Alerts](app-simple-alerts.md)\.

**Important**  
The exercise uses the US East \(N\. Virginia\) Region \(us\-east\-1\) to set up the application\. You can use any of the supported AWS Regions\.

**Next Step**  
[Step 3\.1: Create an Application](get-started-create-app.md)