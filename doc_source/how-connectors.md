# Using Connectors to Move Data in Kinesis Data Analytics for Apache Flink With the DataStream API<a name="how-connectors"></a>

In the Amazon Kinesis Data Analytics for Apache Flink DataStream API, *connectors* are software components that move data into and out of a Kinesis Data Analytics application\. Connectors are flexible integrations that enable you to read from files and directories\. Connectors consist of complete modules for interacting with Amazon services and third\-party systems\.

Types of connectors include the following:
+ [Sources](how-sources.md): Provide data to your application from a Kinesis data stream, file, or other data source\.
+ [Sinks](how-sinks.md): Send data from your application to a Kinesis data stream, Kinesis Data Firehose delivery stream, or other data destination\.
+ [Asynchronous I/O](how-async.md): Provides asynchronous access to a data source \(such as a database\) to enrich stream events\. 

## Available Connectors<a name="how-connectors-list"></a>

The Apache Flink framework contains connectors for accessing data from a variety of sources\. For information about connectors available in the Apache Flink framework, see [Connectors](https://nightlies.apache.org/flink/flink-docs-release-1.15/dev/connectors/) in the [Apache Flink documentation](https://nightlies.apache.org/flink/flink-docs-release-1.15/)\.

**Warning**  
If you have applications running on Flink 1\.6, 1\.8, 1\.11 or 1\.13 and would like to run in Middle East \(UAE\) or Asia Pacific \(Jakarta\) Regions you may need to rebuild your application archive with an updated connector or upgrade to Flink 1\.15\. Following are recommended guidelines:   


**Connector upgrades**  

| Flink version | Connector used | Resolution | 
| --- | --- | --- | 
| 1\.6 \- 1\.13 | Firehose | Your application depends on an outdated version of Firehose connector that is not aware of newer AWS Regions\. Rebuild your application archive with Firehose connector version 2\.1\.0\. [v2\.1\.0](https://github.com/aws/aws-kinesisanalytics-flink-connectors/releases/tag/2.1.0) | 
| 1\.8 | Kinesis | Your application depends on an outdated version of Flink Kinesis connector that is not aware of newer AWS Regions\. Rebuild your application archive with Flink Kinesis connector version 1\.6\.1\. [https://github\.com/awslabs/amazon\-kinesis\-connector\-flink/tree/1\.6\.1](https://github.com/awslabs/amazon-kinesis-connector-flink/tree/1.6.1) | 
| 1\.11 | Kinesis | Your application depends on an outdated version of Flink Kinesis connector that is not aware of newer AWS Regions\. Rebuild your application archive with Flink Kinesis connector version 2\.4\.1\. [https://github\.com/awslabs/amazon\-kinesis\-connector\-flink/tree/2\.4\.1](https://github.com/awslabs/amazon-kinesis-connector-flink/tree/2.4.1) | 
| 1\.6 and 1\.13 | Kinesis | Your application depends on an outdated version of Flink Kinesis connector that is not aware of newer AWS Regions\. Unfortunately, Flink no longer releases patches or bug fixes for 1\.6/1\.13 connectors\. We suggest updating to Flink 1\.15 by rebuilding your application archive with Flink 1\.15\.  | 