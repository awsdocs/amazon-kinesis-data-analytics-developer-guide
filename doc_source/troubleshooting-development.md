# Development Troubleshooting<a name="troubleshooting-development"></a>

**Topics**
+ [Apache Flink Flame Graphs](#troubleshooting-update-flamegraphs)
+ [Issue with EFO connector 1\.15\.2](#troubleshooting-update-efo-connector)
+ [Compile Error: "Could not resolve dependencies for project"](#troubleshooting-compile)
+ [Invalid Choice: "kinesisanalyticsv2"](#troubleshooting-cli-update)
+ [UpdateApplication Action Isn't Reloading Application Code](#troubleshooting-update)

## Apache Flink Flame Graphs<a name="troubleshooting-update-flamegraphs"></a>

Flame Graphs are enabled by default on applications in Kinesis Data Analytics for Apache Flink versions that support it\. Flame Graphs may affect application performance if you keep the graph open, as mentioned in [Flink documentation](https://nightlies.apache.org/flink/flink-docs-release-1.15/docs/ops/debugging/flame_graphs/)\. 

 If you want to disable Flame Graphs for your application, create a case to request it to be disabled for your application ARN\. For more information, see the [AWS Support Center](https://console.aws.amazon.com/support/home#/)\.

## Issue with EFO connector 1\.15\.2<a name="troubleshooting-update-efo-connector"></a>

There is a known issue with the 1\.15\.2 Kinesis Data Streams EFO connector showing performance degradation if the application suffers from high backpressure\. To mitigate, use the Flink 1\.13\.3 connector\. You can use the [1\.13\-SNAPSHOT](https://mvnrepository.com/artifact/com.daml/build-info_2.13/1.13.0-snapshot.20210504.6833.0.9ae787d0) on Maven\. 

## Compile Error: "Could not resolve dependencies for project"<a name="troubleshooting-compile"></a>

In order to compile the Kinesis Data Analytics for Apache Flink sample applications, you must first download and compile the Apache Flink Kinesis connector and add it to your local Maven repository\. If the connector hasn't been added to your repository, a compile error similar to the following appears:

```
Could not resolve dependencies for project your project name: Failure to find org.apache.flink:flink-connector-kinesis_2.11:jar:1.8.2 in https://repo.maven.apache.org/maven2 was cached in the local repository, resolution will not be reattempted until the update interval of central has elapsed or updates are forced
```

To resolve this error, you must download the Apache Flink source code \(version 1\.8\.2 from [https://flink\.apache\.org/downloads\.html](https://flink.apache.org/downloads.html)\) for the connector\. For instructions about how to download, compile, and install the Apache Flink source code, see [Using the Apache Flink Kinesis Streams Connector with previous Apache Flink versions](earlier.md#how-creating-apps-building-kinesis)\.

## Invalid Choice: "kinesisanalyticsv2"<a name="troubleshooting-cli-update"></a>

To use v2 of the Kinesis Data Analytics API, you need the latest version of the AWS Command Line Interface \(AWS CLI\)\.

For information about upgrading the AWS CLI, see [ Installing the AWS Command Line Interface](https://docs.aws.amazon.com/cli/latest/userguide/installing.html) in the *AWS Command Line Interface User Guide*\.

## UpdateApplication Action Isn't Reloading Application Code<a name="troubleshooting-update"></a>

The [UpdateApplication](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_UpdateApplication.html) action will not reload application code with the same file name if no S3 object version is specified\. To reload application code with the same file name, enable versioning on your S3 bucket, and specify the new object version using the `ObjectVersionUpdate` parameter\. For more information about enabling object versioning in an S3 bucket, see [Enabling or Disabling Versioning](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/enable-versioning.html)\.