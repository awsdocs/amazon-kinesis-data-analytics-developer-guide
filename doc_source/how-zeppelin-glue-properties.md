# Table properties<a name="how-zeppelin-glue-properties"></a>

In addition to data fields, your AWS Glue tables provide other information to your Studio notebook using table properties\. Kinesis Data Analytics uses the following AWS Glue table properties:
+ [Using Apache Flink time values](#how-zeppelin-glue-timestamp): These properties define how Kinesis Data Analytics emits Apache Flink internal data processing time values\.
+ [Using Flink Connector and format properties](#how-zeppelin-glue-connector): These properties provide information about your data streams\.

To add a property to an AWS Glue table, do the following:

1. Sign in to the AWS Management Console and open the AWS Glue console at [https://console\.aws\.amazon\.com/glue/](https://console.aws.amazon.com/glue/)\.

1. From the list of tables, choose the table that your application uses to store its data connection information\. Choose **Action**, **Edit table details**\.

1. Under **Table Properties**, enter **kinesisanalytics\.proctime** for **key** and **user\_action\_time** for **Value**\.

## Using Apache Flink time values<a name="how-zeppelin-glue-timestamp"></a>

Apache Flink provides time values that describe when stream processing events occured, such as [ Processing Time](https://nightlies.apache.org/flink/flink-docs-release-1.15/dev/table/streaming/time_attributes.html#processing-time) and [ Event Time](https://nightlies.apache.org/flink/flink-docs-release-1.15/dev/table/streaming/time_attributes.html#event-time)\. To include these values in your application output, you define properties on your AWS Glue table that tell the Kinesis Data Analytics runtime to emit these values into the specified fields\. 

The keys and values you use in your table properties are as follows:


| Timestamp Type | Key | Value | 
| --- |--- |--- |
| [ Processing Time](https://nightlies.apache.org/flink/flink-docs-release-1.15/dev/table/streaming/time_attributes.html#processing-time) | kinesisanalytics\.proctime | The column name that AWS Glue will use to expose the value\. This column name does not correspond to an existing table column\. | 
| [ Event Time](https://nightlies.apache.org/flink/flink-docs-release-1.15/dev/table/streaming/time_attributes.html#event-time) | kinesisanalytics\.rowtime | The column name that AWS Glue will use to expose the value\. This column name corresponds to an existing table column\. | 
| kinesisanalytics\.watermark\.*column\_name*\.milliseconds | The watermark interval in milliseconds | 

## Using Flink Connector and format properties<a name="how-zeppelin-glue-connector"></a>

You provide information about your data sources to your application's Flink connectors using AWS Glue table properties\. Some examples of the properties that Kinesis Data Analytics uses for connectors are as follows:


| Connector Type | Key | Value | 
| --- |--- |--- |
| [ Kafka](https://nightlies.apache.org/flink/flink-docs-release-1.15/dev/table/connectors/kafka.html#connector-options) | format | The format used to deserialize and serialize Kafka messages, e\.g\. json or csv\. | 
| scan\.startup\.mode | The startup mode for the Kafka consumer, e\.g\. earliest\-offset or timestamp\. | 
| [ Kinesis](https://ci.apache.org/projects/flink/flink-docs-release-1.12/dev/table/connectors/kinesis.html#connector-options) | format | The format used to deserialize and serialize Kinesis data stream records, e\.g\. json or csv\. | 
| aws\.region | The AWS region where the stream is defined\.  | 
| [ S3 \(Filesystem\)](https://ci.apache.org/projects/flink/flink-docs-release-1.12/dev/table/connectors/filesystem.html) | format | The format used to deserialize and serialize files, e\.g\. json or csv\. | 
| path | The Amazon S3 path, e\.g\. s3://mybucket/\. | 

For more information about other connectors besides Kinesis and Apache Kafka, see your connector's documentation\.