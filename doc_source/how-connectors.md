# Using Connectors to Move Data in Kinesis Data Analytics for Apache Flink With the DataStream API<a name="how-connectors"></a>

In the Amazon Kinesis Data Analytics for Apache Flink DataStream API, *connectors* are software components that move data into and out of a Kinesis Data Analytics application\. Connectors are flexible integrations that enable you to read from files and directories\. Connectors consist of complete modules for interacting with Amazon services and third\-party systems\.

Types of connectors include the following:
+ [Sources](how-sources.md): Provide data to your application from a Kinesis data stream, file, or other data source\.
+ [Sinks](how-sinks.md): Send data from your application to a Kinesis data stream, Kinesis Data Firehose delivery stream, or other data destination\.
+ [Asynchronous I/O](how-async.md): Provides asynchronous access to a data source \(such as a database\) to enrich stream events\. 

## Available Connectors<a name="how-connectors-list"></a>

The Apache Flink framework contains connectors for accessing data from a variety of sources\. For information about connectors available in the Apache Flink framework, see [Connectors](https://ci.apache.org/projects/flink/flink-docs-release-1.11/dev/connectors/) in the [Apache Flink documentation](https://ci.apache.org/projects/flink/flink-docs-release-1.11/)\.