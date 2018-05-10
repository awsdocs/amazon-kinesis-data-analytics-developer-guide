# Best Practices<a name="best-practices"></a>

This section describes best practices when working with Amazon Kinesis Data Analytics applications\.

**Topics**
+ [Managing Applications](#bp-manage-apps)
+ [Defining Input Schema](#bp-define-inputschema)
+ [Connecting to Outputs](#bp-connect-to-outputs)
+ [Authoring Application Code](#bp-authoring-sqlcode)

## Managing Applications<a name="bp-manage-apps"></a>

When managing Amazon Kinesis Data Analytics applications, follow these best practices:
+ **Set up CloudWatch alarms** – Using the CloudWatch metrics that Amazon Kinesis Data Analytics provides, you can monitor the following:
  + Input bytes and input records \(number of bytes and records entering the application\)
  + Output bytes, output record 
  + `MillisBehindLatest` \(tracks how far behind the application is in reading from the streaming source\)

  We recommend that you set up at least two CloudWatch alarms on the following metrics for your in\-production applications:

   
  + Alarm on `MillisBehindLatest` – For most cases, we recommend that you set this alarm to trigger when your application is one hour behind the latest data, for an average of one minute\. For applications with lower end\-to\-end processing needs, you can tune this to a lower tolerance\. The alarm can help you ensure that your application is reading the latest data\. 

     
+ Limit the number of production applications reading from the same Kinesis stream to two applications to avoid getting the `ReadProvisionedThroughputException` exception\.
**Note**  
In this case, the term *application* refers to any application that can read from the streaming source\. Only an Amazon Kinesis Data Analytics application can read from a Kinesis Data Firehose delivery stream\. However, many applications can read from an Kinesis stream, such as an Amazon Kinesis Data Analytics application or AWS Lambda\. The recommended application limit refers to all applications that you configure to read from a streaming source\.

   

  Amazon Kinesis Data Analytics reads a streaming source approximately once per second per application\. However, an application that falls behind might read data at a faster rate to catch up\. To allow adequate throughput for applications to catch up, you limit the number of applications reading the same data source\.
+ Limit the number of production applications reading from the same Kinesis Data Firehose delivery stream to one application\.

  A Kinesis Data Firehose delivery stream can write to destinations such as Amazon S3, Amazon Redshift, and it can also be a streaming source for your Amazon Kinesis Data Analytics application\. Therefore, we recommend you do not configure more than one Amazon Kinesis Data Analytics application per Kinesis Data Firehose delivery stream to make sure the delivery stream can also deliver to other destinations\.

## Defining Input Schema<a name="bp-define-inputschema"></a>

When configuring application input in the console, you first specify a streaming source\. The console then uses the discovery API \(see [DiscoverInputSchema](API_DiscoverInputSchema.md)\) to infer a schema by sampling records on the streaming source\. The schema, among other things, defines names and data types of the columns in the resulting in\-application stream\. The console displays the schema\. We recommend you do the following with this inferred schema:
+ Adequately test the inferred schema\. The discovery process uses only a sample of records on the streaming source to infer a schema\. If your streaming source has [many record types](http://docs.aws.amazon.com/kinesisanalytics/latest/dev/app-tworecordtypes.html), there is a possibility that the discovery API missed sampling one or more record types, which can result in a schema that does not accurately reflect data on the streaming source\. 

   

  When your application starts, these missed record types might result in parsing errors\. Amazon Kinesis Data Analytics sends these records to the in\-application error stream\. To reduce these parsing errors, we recommend that you test the inferred schema interactively in the console, and monitor the in\-application stream for missed records\. 

   
+ The Amazon Kinesis Data Analytics API does not support specifying the `NOT NULL` constraint on columns in the input configuration\. If you want `NOT NULL` constraints on columns in your in\-application stream, you should create these in\-application streams using your application code\. You can then copy data from one in\-application stream into another, and then the constraint will be enforced\.

   

  Any attempt to insert rows with `NULL` values when a value is required results in an error, and Amazon Kinesis Data Analytics sends these errors to the in\-application error stream\. 

   
+ Relax data types inferred by the discovery process\. The discovery process recommends columns and data types based on a random sampling of records on the streaming source\. We recommend that you review these carefully and consider relaxing these data types to cover all of the possible cases of records in your input\. This ensures fewer parsing errors across the application while it is running\. For example, if inferred schema has a `SMALLINT` as column type, perhaps consider changing it to `INTEGER`\.

   
+ Use SQL functions in your application code to handle any unstructured data or columns\. You may have unstructured data or columns, such as log data, in your input\. For examples, see [Example: Manipulating Strings and Date Times](app-string-datetime-manipulation.md)\. One approach to handling this type of data is to define the schema with only one column of type `VARCHAR(N)`, where `N` is the largest possible row that you would expect to see in your stream\. In your application code you can then read the incoming records, use the `String` and `Date Time` functions to parse and schematize the raw data\. 

   
+ Make sure that you handle streaming source data that contains nesting more than two levels deep completely\. When source data is JSON, you can have nesting\. The discovery API will infer a schema that flattens one level of nesting\. For two levels of nesting, the discovery API will also attempt to flatten these\. Beyond two levels of nesting, there is limited support for flattening\. In order to handle nesting completely, you have to manually modify the inferred schema to suite your needs\. Use either of the following strategies to do this:

   
  +  Use the JSON row path to selectively pull out only the required key value pairs for your application\. A JSON row path provides a pointer to the specific key value pair you would like to bring in your application\. This can be done for any level of nesting\.

     
  + Use the JSON row path to selectively pull out complex JSON objects and then use string manipulation functions in your application code to pull the specific data that you need\.

## Connecting to Outputs<a name="bp-connect-to-outputs"></a>

We recommend that every application have at least two outputs\. use the first destination to insert the results of your SQL queries\. Use the second destination to insert the entire error stream and send it to an S3 bucket through a Amazon Kinesis Data Firehose delivery stream\. 

## Authoring Application Code<a name="bp-authoring-sqlcode"></a>

We recommend the following:
+ In your SQL statement, we recommend that you do not specify time\-based window that is longer than one hour for the following reasons:
  + If an application needs to be restarted, either because you updated the application or for Amazon Kinesis Data Analytics internal reasons, all data included in the window must be read again from the streaming data source\. This will take time before Amazon Kinesis Data Analytics can emit output for that window\. 
  + Amazon Kinesis Data Analytics must maintain everything related to the application's state, including relevant data, for the duration\. This will consume significant Amazon Kinesis Data Analytics processing units\. 
+ During development, keep window size small in your SQL statements so that you can see the results faster\. When you deploy the application to your production environment, you can set the window size as appropriate\.
+ Instead of a single complex SQL statement, you might consider breaking it into multiple statements, in each step saving results in intermediate in\-application streams\. This might help you debug faster\.
+ When using [tumbling windows](http://docs.aws.amazon.com/kinesisanalytics/latest/dev/tumbling-window-concepts.html), we recommend that you use two windows, one for processing time and one for your logical time \(ingest time or event time\)\. For more information, see [Timestamps and the ROWTIME Column](timestamps-rowtime-concepts.md)\.