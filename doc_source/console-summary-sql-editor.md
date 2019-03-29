# Working with the SQL Editor<a name="console-summary-sql-editor"></a>

Following, you can find information about sections of the SQL editor and how each works\. In the SQL editor, you can either author your own code yourself or choose **Add SQL from templates**\. A SQL template gives you example SQL code that can help you write common Amazon Kinesis Data Analytics applications\. The example applications in this guide use some of these templates\. For more information, see [Example Applications](examples.md)\.

![\[Screenshot of the SQL editor showing the real-time analytics tab and in-application streams.\]](http://docs.aws.amazon.com/kinesisanalytics/latest/dev/images/gs-v2-50.png)

## Source Data Tab<a name="console-summary-source-data"></a>

The **Source data** tab identifies a streaming source\. It also identifies the in\-application input stream that this source maps to and that provides the application input configuration\. 

![\[Screenshot of the SQL editor showing the source data tab with the streaming source highlighted.\]](http://docs.aws.amazon.com/kinesisanalytics/latest/dev/images/gs-v2-60.png)

Amazon Kinesis Data Analytics provides the following timestamp columns, so that you don't need to provide explicit mapping in your input configuration: 
+ **ROWTIME** – Each row in an in\-application stream has a special column called `ROWTIME`\. This column is the timestamp for the point when Kinesis Data Analytics inserted the row in the first in\-application stream\. 
+ **Approximate\_Arrival\_Time** – Records on your streaming source include the `Approximate_Arrival_Timestamp` column\. It is the approximate arrival timestamp that is set when the streaming source successfully receives and stores the related record\. Kinesis Data Analytics fetches this column into the in\-application input stream as `Approximate_Arrival_Time`\. Amazon Kinesis Data Analytics provides this column only in the in\-application input stream that is mapped to the streaming source\. 

These timestamp values are useful in windowed queries that are time\-based\. For more information, see [Windowed Queries](windowed-sql.md)\.

## Real\-Time Analytics Tab<a name="console-summary-realtime-analytics"></a>

The **Real\-time analytics** tab shows all the in\-application streams that your application code creates\. This group of streams includes the error stream \(`error_stream`\) that Amazon Kinesis Data Analytics provides for all applications\. 

![\[Screenshot of the SQL editor showing the real-time analytics tab with in-application streams highlighted.\]](http://docs.aws.amazon.com/kinesisanalytics/latest/dev/images/gs-v2-70.png)

## Destination Tab<a name="console-summary-destination"></a>

The **Destination** tab enables you to configure the application output to persist in\-application streams to external destinations\. You can configure output to persist data in any of the in\-application streams to external destinations\. For more information, see [Configuring Application Output](how-it-works-output.md)\.