# Example: Explore the In\-Application Error Stream<a name="app-explore-error-stream"></a>

Amazon Kinesis Data Analytics provides an in\-application error stream for each application you create\. Any rows that your application cannot process are sent to this error stream\. You might consider persisting the error stream data to an external destination so that you can investigate\. 

In this exercise, you introduce errors in input configuration by editing the schema inferred by the discovery process, and verify rows sent to the error stream\.

You perform this exercise in the console\.

## Introduce Parse Error<a name="intro-error-parse-error"></a>

In this exercise, you introduce a parse error\.

1. Create an application\. For instructions, see [Step 3\.1: Create an Application](get-started-create-app.md)\. 

1. On the newly created application hub, choose **Connect to a source**\.

1. On the **Source** page, select the demo stream \(`kinesis-anlaytics-demo-stream`\)\.

   If you followed the Getting Started exercise, you have a demo stream in your account\.

1. Amazon Kinesis Data Analytics takes a sample from the demo stream to infer a schema for the in\-application input stream it creates\. The console shows the inferred schema and sample data in the **Formatted stream sample** tab\.

1. Now you edit the schema and modify the column type to introduce the parse error\. Choose **Edit schema**\.

1. Change the `TICKER_SYMBOL` column type from `VARCHAR(4)` to `INTEGER`\. 

   Now that column type of the in\-application schema that is created is invalid, Amazon Kinesis Data Analytics can't bring in data in the in\-application stream\. Instead Kinesis Data Analytics sends the rows to the error stream\.

1. Choose **Save schema**\.

1. Choose **Refresh schema samples**\.

   Notice that there are no rows in the **Formatted stream** sample\. However, the **Error stream** tab shows data with an error message\. The **Error stream** tab shows data sent to the in\-application error stream\. 

   Because you changed the column data type, Amazon Kinesis Data Analytics was not able to bring the data in the in\-application input stream, and instead it sent the data to the error stream\.

## Divide by Zero Error<a name="intro-error-divide-zero"></a>

In this exercise, you update the application code to introduce a runtime error \(division by zero\)\. Notice that Amazon Kinesis Data Analytics sends the resulting rows to the in\-application error stream, not to the in\-application error stream where the results are supposed to be written\.

1. Follow the Getting Started exercise to create an application\. For instructions, see [ Step 3: Create Your Starter Amazon Kinesis Data Analytics Application](get-started-exercise.md)\.

   Verify the results on the **Real\-time analytics** tab as follows:

   Sour

1. Update the `SELECT` statement in the application code to introduce divide by zero\. For example: 

   ```
   SELECT STREAM ticker_symbol, sector, change, (price / 0) as ProblemColumn
   FROM "SOURCE_SQL_STREAM_001"
   WHERE sector SIMILAR TO '%TECH%';
   ```

1. Run the application\. Because of the division by zero runtime error occurs, instead of writing the results to the `DESTINATION_SQL_STREAM`, Amazon Kinesis Data Analytics sends rows to the in\-application error stream\. On the **Real\-time analytics** tab, choose the error\-stream, and then you can see the rows in the in\-application error stream\. 