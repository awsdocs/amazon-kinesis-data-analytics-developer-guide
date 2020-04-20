# Example: Exploring the In\-Application Error Stream<a name="app-explore-error-stream"></a>

Amazon Kinesis Data Analytics provides an in\-application error stream for each application that you create\. Any rows that your application cannot process are sent to this error stream\. You might consider persisting the error stream data to an external destination so that you can investigate\. 

You perform the following exercises on the console\. In these examples, you introduce errors in the input configuration by editing the schema that is inferred by the discovery process, and then you verify the rows that are sent to the error stream\.

**Topics**
+ [Introducing a Parse Error](#intro-error-parse-error)
+ [Introducing a Divide by Zero Error](#intro-error-divide-zero)

## Introducing a Parse Error<a name="intro-error-parse-error"></a>

In this exercise, you introduce a parse error\.

1. Create a Kinesis data analytics application as described in the Kinesis Data Analytics [Getting Started](https://docs.aws.amazon.com/kinesisanalytics/latest/dev/get-started-exercise.html) exercise\. 

1. On the application details page, choose **Connect streaming data**\.

1. If you followed the Getting Started exercise, you have a demo stream \(`kinesis-analytics-demo-stream`\) in your account\. On the **Connect to source** page, choose this demo stream\.

1. Kinesis Data Analytics takes a sample from the demo stream to infer a schema for the in\-application input stream it creates\. The console shows the inferred schema and sample data in the **Formatted stream sample** tab\.

1. Next, edit the schema and modify the column type to introduce the parse error\. Choose **Edit schema**\.

1. Change the `TICKER_SYMBOL` column type from `VARCHAR(4)` to `INTEGER`\. 

   Now that the column type of the in\-application schema that is created is invalid, Kinesis Data Analytics can't bring in data in the in\-application stream\. Instead, it sends the rows to the error stream\.

1. Choose **Save schema**\.

1. Choose **Refresh schema samples**\.

   Notice that there are no rows in the **Formatted stream** sample\. However, the **Error stream** tab shows data with an error message\. The **Error stream** tab shows data sent to the in\-application error stream\. 

   Because you changed the column data type, Kinesis Data Analytics could not bring the data in the in\-application input stream\. It sent the data to the error stream instead\.

## Introducing a Divide by Zero Error<a name="intro-error-divide-zero"></a>

In this exercise, you update the application code to introduce a runtime error \(division by zero\)\. Notice that Amazon Kinesis Data Analytics sends the resulting rows to the in\-application error stream, not to the `DESTINATION_SQL_STREAM` in\-application stream where the results are supposed to be written\.

1. Create a Kinesis data analytics application as described in the Kinesis Data Analytics [Getting Started](https://docs.aws.amazon.com/kinesisanalytics/latest/dev/get-started-exercise.html) exercise\.

   Verify the results on the **Real\-time analytics** tab as follows:

   Sour

1. Update the `SELECT` statement in the application code to introduce divide by zero; for example: 

   ```
   SELECT STREAM ticker_symbol, sector, change, (price / 0) as ProblemColumn
   FROM "SOURCE_SQL_STREAM_001"
   WHERE sector SIMILAR TO '%TECH%';
   ```

1. Run the application\.

   Because the division by zero runtime error occurs, instead of writing the results to the `DESTINATION_SQL_STREAM`, Kinesis Data Analytics sends rows to the in\-application error stream\. On the **Real\-time analytics** tab, choose the error stream, and then you can see the rows in the in\-application error stream\. 