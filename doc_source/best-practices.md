# Best Practices<a name="best-practices"></a>

This section describes best practices when working with Amazon Kinesis Data Analytics applications\.

**Topics**
+ [Managing Applications](#bp-manage-apps)
+ [Scaling Applications](#bp-scale-apps)
+ [Defining Input Schema](#bp-define-inputschema)
+ [Connecting to Outputs](#bp-connect-to-outputs)
+ [Authoring Application Code](#bp-authoring-sqlcode)
+ [Testing Applications](#bp-testing)

## Managing Applications<a name="bp-manage-apps"></a>

When managing Amazon Kinesis Data Analytics applications, follow these best practices:
+ Set up Amazon CloudWatch alarms – You can use the CloudWatch metrics that Kinesis Data Analytics provides to monitor the following:
  + Input bytes and input records \(number of bytes and records entering the application\)
  + Output bytes and output records 
  + `MillisBehindLatest` \(how far behind the application is in reading from the streaming source\)

  We recommend that you set up at least two CloudWatch alarms on the following metrics for your in\-production applications:
  + `MillisBehindLatest` – For most cases, we recommend that you set this alarm to trigger when your application is 1 hour behind the latest data, for an average of 1 minute\. For applications with lower end\-to\-end processing needs, you can tune this to a lower tolerance\. This alarm can help ensure that your application is reading the latest data\. 

     
+ To avoid getting the `ReadProvisionedThroughputException` exception, limit the number of production applications reading from the same Kinesis data stream to two applications\.
**Note**  
In this case, *application* refers to any application that can read from the streaming source\. Only a Kinesis Data Analytics application can read from a Kinesis Data Firehose delivery stream\. However, many applications can read from a Kinesis data stream, such as a Kinesis Data Analytics application or AWS Lambda\. The recommended application limit refers to all applications that you configure to read from a streaming source\.

   

  Amazon Kinesis Data Analytics reads a streaming source approximately once per second per application\. However, an application that falls behind might read data at a faster rate to catch up\. To allow adequate throughput for applications to catch up, limit the number of applications reading the same data source\.

   
+ Limit the number of production applications reading from the same Kinesis Data Firehose delivery stream to one application\.

  A Kinesis Data Firehose delivery stream can write to destinations such as Amazon S3 and Amazon Redshift\. It can also be a streaming source for your Kinesis Data Analytics application\. Therefore, we recommend that you do not configure more than one Kinesis Data Analytics application per Kinesis Data Firehose delivery stream\. This helps ensure that the delivery stream can also deliver to other destinations\.

## Scaling Applications<a name="bp-scale-apps"></a>

Set up your application for your future scaling needs by proactively increasing the number of input in\-application streams from the default \(one\)\. We recommend the following language choices based on the throughput of your application: 
+ Use multiple streams and Kinesis Data Analytics for SQL applications if your application has scaling needs beyond 100 MB/second\.
+ Use [Kinesis Data Analytics for Java Applications](/kinesisanalytics/latest/java/what-is.html) if you want to use a single stream and application\.

## Defining Input Schema<a name="bp-define-inputschema"></a>

When configuring application input in the console, you first specify a streaming source\. The console then uses the discovery API \(see [DiscoverInputSchema](API_DiscoverInputSchema.md)\) to infer a schema by sampling records on the streaming source\. The schema, among other things, defines names and data types of the columns in the resulting in\-application stream\. The console displays the schema\. We recommend that you do the following with this inferred schema:
+ Adequately test the inferred schema\. The discovery process uses only a sample of records on the streaming source to infer a schema\. If your streaming source has [many record types](https://docs.aws.amazon.com/kinesisanalytics/latest/dev/app-tworecordtypes.html), the discovery API might have missed sampling one or more record types\. This situation can result in a schema that does not accurately reflect data on the streaming source\. 

  When your application starts, these missed record types might result in parsing errors\. Amazon Kinesis Data Analytics sends these records to the in\-application error stream\. To reduce these parsing errors, we recommend that you test the inferred schema interactively in the console and monitor the in\-application stream for missed records\. 

   
+ The Kinesis Data Analytics API does not support specifying the `NOT NULL` constraint on columns in the input configuration\. If you want `NOT NULL` constraints on columns in your in\-application stream, create these in\-application streams using your application code\. You can then copy data from one in\-application stream into another, and then the constraint is enforced\.

  Any attempt to insert rows with `NULL` values when a value is required results in an error\. Kinesis Data Analytics sends these errors to the in\-application error stream\. 

   
+ Relax data types inferred by the discovery process\. The discovery process recommends columns and data types based on a random sampling of records on the streaming source\. We recommend that you review these carefully and consider relaxing these data types to cover all of the possible cases of records in your input\. This ensures fewer parsing errors across the application while it is running\. For example, if an inferred schema has a `SMALLINT` as a column type, consider changing it to an `INTEGER`\.

   
+ Use SQL functions in your application code to handle any unstructured data or columns\. You might have unstructured data or columns, such as log data, in your input\. For examples, see [Example: Transforming DateTime Values](app-string-datetime-manipulation.md)\. One approach to handling this type of data is to define the schema with only one column of type `VARCHAR(N)`, where `N` is the largest possible row that you would expect to see in your stream\. In your application code, you can then read the incoming records and use the `String` and `Date Time` functions to parse and schematize the raw data\. 

   
+ Make sure that you completely handle streaming source data that contains nesting more than two levels deep\. When source data is JSON, you can have nesting\. The discovery API infers a schema that flattens one level of nesting\. For two levels of nesting, the discovery API also tries to flatten these\. Beyond two levels of nesting, there is limited support for flattening\. To handle nesting completely, you have to manually modify the inferred schema to suit your needs\. Use either of the following strategies to do this:

   
  +  Use the JSON row path to selectively pull out only the required key value pairs for your application\. A JSON row path provides a pointer to the specific key value pair that you want to bring in your application\. You can do this for any level of nesting\.
  + Use the JSON row path to selectively pull out complex JSON objects and then use string manipulation functions in your application code to pull the specific data that you need\.

## Connecting to Outputs<a name="bp-connect-to-outputs"></a>

We recommend that every application have at least two outputs: 
+ Use the first destination to insert the results of your SQL queries\. 
+ Use the second destination to insert the entire error stream and send it to an S3 bucket through a Kinesis Data Firehose delivery stream\.

## Authoring Application Code<a name="bp-authoring-sqlcode"></a>

We recommend the following:
+ In your SQL statement, don't specify a time\-based window that is longer than one hour for the following reasons:
  + Sometimes an application needs to be restarted, either because you updated the application or for Kinesis Data Analytics internal reasons\. When it restarts, all data included in the window must be read again from the streaming data source\. This takes time before Kinesis Data Analytics can emit output for that window\. 
  + Kinesis Data Analytics must maintain everything related to the application's state, including relevant data, for the duration\. This consumes significant Kinesis Data Analytics processing units\. 
+ During development, keep the window size small in your SQL statements so that you can see the results faster\. When you deploy the application to your production environment, you can set the window size as appropriate\.
+ Instead of a single complex SQL statement, consider breaking it into multiple statements, in each step saving results in intermediate in\-application streams\. This might help you debug faster\.
+ When you're using [tumbling windows](https://docs.aws.amazon.com/kinesisanalytics/latest/dev/tumbling-window-concepts.html), we recommend that you use two windows, one for processing time and one for your logical time \(ingest time or event time\)\. For more information, see [Timestamps and the ROWTIME Column](timestamps-rowtime-concepts.md)\.

## Testing Applications<a name="bp-testing"></a>

When you're changing the schema or application code for your Kinesis Data Analytics application, we recommend using a test application to verify your changes before deploying them to production\.

### Setting up a Test Application<a name="bp-testing-setup"></a>

You can set up a test application either through the console, or by using an AWS CloudFormation template\. Using an AWS CloudFormation template helps ensure that the code changes you make to the test application and your live application are consistent\.

When setting up a test application, you can either connect the application to your live data, or you can populate a stream with mock data to test against\. We recommend two methods for populating a stream with mock data:
+ Use the [Kinesis Data Generator \(KDG\)](http://aws.amazon.com/blogs/big-data/test-your-streaming-data-solution-with-the-new-amazon-kinesis-data-generator/)\. The KDG uses a data template to send random data to a Kinesis stream\. The KDG is simple to use, but isn't appropriate for testing complex relationships between data items, such as for applications that detect data hotspots or anomalies\.
+ Use a custom Python application to send more complex data to a Kinesis data stream\. A Python application can generate complex relationships between data items, such as hotspots or anomalies\. For an example of a Python application that sends data clustered into a data hotspot, see [Example: Detecting Hotspots on a Stream \(HOTSPOTS Function\)](app-hotspots-detection.md)\.

When running your test application, view your results using a destination \(such as a Kinesis Data Firehose delivery stream to an Amazon Redshift database\) instead of viewing your in\-application stream on the console\. The data that is displayed on the console is a sampling of the stream and doesn't contain all of the records\.

### Testing Schema Changes<a name="bp-testing-schema"></a>

When changing an application's input stream schema, use your test application to verify that the following are true:
+ The data from your stream is being coerced into the correct data type\. For example, ensure that datetime data is not being ingested into the application as a string\.
+ The data is being parsed and coerced into the data type that you want\. If parsing or coercion errors occur, you can view them on the console, or assign a destination to the error stream and view the errors in the destination store\.
+ The data fields for character data are of sufficient length, and the application isn't truncating the character data\. You can check the data records in your destination store to verify that your application data isn't being truncated\.

### Testing Code Changes<a name="bp-testing-code"></a>

Testing changes to your SQL code requires some domain knowledge of your application\. You must be able to determine what output needs to be tested and what the correct output should be\. For potential problem areas to verify when modifying your application's SQL code, see [Troubleshooting Amazon Kinesis Data Analytics for SQL Applications](troubleshooting.md)\.