# What Is Amazon Kinesis Data Analytics for Apache Flink?<a name="what-is"></a>

With Amazon Kinesis Data Analytics for Apache Flink, you can use Java, Scala, or SQL to process and analyze streaming data\. The service enables you to author and run code against streaming sources to perform time\-series analytics, feed real\-time dashboards, and create real\-time metrics\.

You can build Java and Scala applications in Kinesis Data Analytics using open\-source libraries based on [Apache Flink](https://flink.apache.org/)\. Apache Flink is a popular framework and engine for processing data streams\.

**Note**  
Although Kinesis Data Analytics supports Apache Flink applications written in Scala version 2\.12, this guide only contains code examples written in Java\.

Kinesis Data Analytics provides the underlying infrastructure for your Apache Flink applications\. It handles core capabilities like provisioning compute resources, parallel computation, automatic scaling, and application backups \(implemented as checkpoints and snapshots\)\. You can use the high\-level Flink programming features \(such as operators, functions, sources, and sinks\) in the same way that you use them when hosting the Flink infrastructure yourself\.

## Getting Started<a name="what-is-start"></a>

 You can start by creating a Kinesis Data Analytics application that continuously reads and processes streaming data\. Then, author your code using your IDE of choice, and test it with live streaming data\. You can also configure destinations where you want Kinesis Data Analytics to send the results\. 

To get started, we recommend that you read the following sections:
+ [Kinesis Data Analytics for Apache Flink: How It Works](how-it-works.md)
+ [Getting Started with Amazon Kinesis Data Analytics for Apache Flink \(DataStream API\)](getting-started.md)