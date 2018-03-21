# Troubleshooting Amazon Kinesis Data Analytics<a name="troubleshooting"></a>

The following can help you troubleshoot problems you have with Amazon Kinesis Data Analytics\. 

## Get a SQL Statement to Work Correctly<a name="sql-statement"></a>

If you need to figure out how to get a particular SQL statement to work correctly, you have several different resources when using Amazon Kinesis Data Analytics:
+ For more information about SQL statements, see [Example Amazon Kinesis Data Analytics Applications](example-apps.md) in the *Amazon Kinesis Data Analytics Developer Guide\.* This section provides a number of SQL examples that you can use\. 
+ The [Amazon Kinesis Data Analytics SQL Reference](http://docs.aws.amazon.com/kinesisanalytics/latest/sqlref/sqlrf_Preface.html) provides a detailed guide to authoring streaming SQL statements\. 
+ If you are still running into issues, we recommend that you ask a question on the [Kinesis Data Analytics Forums](https://forums.aws.amazon.com/ann.jspa?annID=4153)\. 

## Unable to Detect or Discover My Schema<a name="detect-schema"></a>

In some cases, Kinesis Data Analytics is unable to detect or discover a schema\. In many of these cases, you can still use Kinesis Data Analytics\.

Suppose that you have UTF\-8 encoded data that doesn't use a delimiter, data that uses a format other than comma\-separated value \(CSV\) format, or the discovery API did not discover your schema\. In these cases, you can define a schema by hand or use string manipulation functions to structure your data\. 

To discover the schema for your stream, Kinesis Data Analytics randomly samples the latest data in your stream\. If you aren't consistently sending data to your stream, Kinesis Data Analytics might not be able to retrieve a sample and detect a schema\. For more information, see [Using the Schema Discovery Feature on Streaming Data](sch-dis.md) Using the Schema Discovery Feature and Related Editing in the *Amazon Kinesis Data Analytics Developer Guide\.*

## Important Application Health Parameters to Monitor<a name="parameters"></a>

To make sure that your application is running correctly, we recommend that you monitor certain important parameters\.

The most important parameter to monitor is the Amazon CloudWatch metric `MillisBehindLatest`\. This metric represents how far behind the current time you are reading from the stream\. This metric helps you determine whether you are processing records from the source stream fast enough\. 

As a rule of thumb, you should set up a CloudWatch alarm to trigger if you fall behind more than one hour\. However, the amount of time depends on your use case\. You can adjust it as needed\. 

For more information, see [Best Practices](best-practices.md) in the *Amazon Kinesis Data Analytics Developer Guide\. *

## Invalid Code Errors When Running an Application<a name="invalid-code"></a>

When you cannot save and run the SQL code for your Amazon Kinesis Data Analytics application, the following are common causes:
+ **The stream was redefined in your SQL code** – After you create a stream and the pump associated with the stream, you cannot redefine the same stream in your code\. For more information about creating a stream, see [CREATE STREAM](http://docs.aws.amazon.com/kinesisanalytics/latest/sqlref/sql-reference-create-stream.html)\. For more information about creating a pump, see [CREATE PUMP](http://docs.aws.amazon.com/kinesisanalytics/latest/sqlref/sql-reference-create-pump.html)\.
+ **A GROUP BY clause uses multiple ROWTIME columns ** – You can specify only one ROWTIME column in the GROUP BY clause\. For more information, see [GROUP BY](http://docs.aws.amazon.com/kinesisanalytics/latest/sqlref/sql-reference-group-by-clause.html) and [ROWTIME](http://docs.aws.amazon.com/kinesisanalytics/latest/sqlref/sql-reference-rowtime.html)\. 
+ **One or more data types have an invalid casting ** – In this case, your code has an invalid implicit cast\. For example, you might be casting a timestamp to a bigint in your code\.
+ **A stream has the same name as a service reserved stream name ** – A stream cannot have the same name as the service\-reserved stream `error_stream`\. 

## Application Doesn't Process Data After Deleting and Re\-creating the Kinesis Application Input Stream or Kinesis Data Firehose Delivery Stream with the Same Name<a name="replace-stream"></a>

Suppose that you delete the Kinesis stream that provides application input for a running application and create a new Kinesis stream with the same name\. In this case, the application doesn't process the input data from the new stream\. In addition, no data is delivered to the destination\. 

The same effect occurs if you delete the Kinesis Data Firehose delivery stream for a running application and create a new Kinesis Data Firehose delivery stream with the same name\. 

To resolve this issue, stop and restart the application through the AWS Management Console\.

## Insufficient Throughput or High Latency<a name="insufficient-throughput"></a>

If your application's `MillisBehindLatest` metric is steadily increasing or consistently is above 1000 \(one second\), you can improve your application's throughput\. To do so, increase the value of the `InputParallelism` parameter\. For more information, see [Parallelizing Input Streams for Increased Throughput](input-parallelism.md)\.

If your application is reaching the default limit for Kinesis Processing Units \(KPU\), you can request a limit increase\. For more information, see [Automatically Scaling Applications to Increase Throughput](how-it-works-autoscaling.md)\.