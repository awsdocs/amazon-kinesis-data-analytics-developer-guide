# Creating Kinesis Data Analytics applications with Apache Beam<a name="how-creating-apps-beam"></a>

You can use the [Apache Beam](https://beam.apache.org/) framework with your Kinesis Data Analytics application to process streaming data\. Kinesis Data Analytics applications that use Apache Beam use [Apache Flink runner](https://beam.apache.org/documentation/runners/flink/) to execute Beam pipelines\.

For a tutorial about how to use Apache Beam in a Kinesis Data Analytics application, see [Apache Beam](examples-beam.md)\.

**Topics**
+ [Using Apache Beam with Kinesis Data Analytics](#how-creating-apps-beam-using)
+ [Beam Capabilities](#how-creating-apps-beam-capabilities)

## Using Apache Beam with Kinesis Data Analytics<a name="how-creating-apps-beam-using"></a>

Note the following about using the Apache Flink runner with Kinesis Data Analytics:
+ Apache Beam metrics are not viewable in the Kinesis Data Analytics console\.
+ **Apache Beam is only supported with Kinesis Data Analytics applications that use Apache Flink version 1\.8 and above\. Apache Beam is not supported with Kinesis Data Analytics applications that use Apache Flink version 1\.6\.**

## Beam Capabilities<a name="how-creating-apps-beam-capabilities"></a>

Kinesis Data Analytics supports the same Apache Beam capabilties as the Apache Flink runner\. For information about what features are supported with the Apache Flink runner, see the [Beam Compatibility Matrix](https://beam.apache.org/documentation/runners/capability-matrix/)\. 

We recommend that you test your Apache Flink application in the Kinesis Data Analytics service to verify that we support all the features that your application needs\.