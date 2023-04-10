# Development Troubleshooting<a name="troubleshooting-development"></a>

**Topics**
+ [Apache Flink Flame Graphs](#troubleshooting-update-flamegraphs)
+ [Credential Provider Issue with EFO connector 1\.15\.2](#troubleshooting-credential-provider)
+ [Applications with unsupported Kinesis connectors](#troubleshooting-unsupported-kinesis-connectors)
+ [Compile Error: "Could not resolve dependencies for project"](#troubleshooting-compile)
+ [Invalid Choice: "kinesisanalyticsv2"](#troubleshooting-cli-update)
+ [UpdateApplication Action Isn't Reloading Application Code](#troubleshooting-update)

## Apache Flink Flame Graphs<a name="troubleshooting-update-flamegraphs"></a>

Flame Graphs are enabled by default on applications in Kinesis Data Analytics for Apache Flink versions that support it\. Flame Graphs may affect application performance if you keep the graph open, as mentioned in [Flink documentation](https://nightlies.apache.org/flink/flink-docs-release-1.15//docs/ops/debugging/flame_graphs/)\. 

 If you want to disable Flame Graphs for your application, create a case to request it to be disabled for your application ARN\. For more information, see the [AWS Support Center](https://console.aws.amazon.com/support/home#/)\.

## Credential Provider Issue with EFO connector 1\.15\.2<a name="troubleshooting-credential-provider"></a>

There is a [known issue](https://issues.apache.org/jira/browse/FLINK-29205) with Kinesis Data Streams EFO connector versions up to 1\.15\.2 where the `FlinkKinesisConsumer` is not respecting `Credential Provider` configuration\. Valid configurations are being disregarded due to the issue, which results in the `AUTO` credential provider being used\. This can cause a problem using cross\-account access to Kinesis using EFO connector\.

To resolve this error please use EFO connector version 1\.15\.3 or higher\. 

## Applications with unsupported Kinesis connectors<a name="troubleshooting-unsupported-kinesis-connectors"></a>

Kinesis Data Analytics for Apache Flink version 1\.15 will [automatically reject applications from starting or updating](https://docs.aws.amazon.com/kinesisanalytics/latest/java/flink-1-15-2.html) if they are using unsupported Kinesis Connector versions \(pre\-version 1\.15\.2\) bundled into application JARs or archives \(ZIP\)\. 

### Rejection Error<a name="troubleshooting-unsupported-kinesis-connectors-error"></a>

You will see the following error when submitting create / update application calls through:

```
An error occurred (InvalidArgumentException) when calling the CreateApplication operation: An unsupported Kinesis connector version has been detected in the application. Please update flink-connector-kinesis to any version equal to or newer than 1.15.2.
For more information refer to connector fix: https://issues.apache.org/jira/browse/FLINK-23528
```

### Steps to remediate<a name="troubleshooting-unsupported-kinesis-connectors-steps-to-remediate"></a>
+ Update the application’s dependency on `flink-connector-kinesis`\. If you are using Maven as your project’s build tool, follow [Update a Maven dependency ](#troubleshooting-unsupported-kinesis-connectors-update-maven-dependency)\. If you are using Gradle, follow [Update a Gradle dependency ](#troubleshooting-unsupported-kinesis-connectors-update-gradle-dependency)\.
+ Repackage the application\.
+ Upload to an Amazon S3 bucket\.
+ Resubmit the create / update application request with the revised application just uploaded to the Amazon S3 bucket\.
+ If you continue to see the same error message, re\-check your application dependencies\. If the problem persists please create a support ticket\. 

#### Update a Maven dependency<a name="troubleshooting-unsupported-kinesis-connectors-update-maven-dependency"></a>

1. Open the project’s `pom.xml`\.

1. Find the project’s dependencies\. They look like:

   ```
   <project>
   
       ...
   
       <dependencies>
   
           ...
   
           <dependency>
               <groupId>org.apache.flink</groupId>
               <artifactId>flink-connector-kinesis</artifactId>
           </dependency>
   
           ...
   
       </dependencies>
   
       ...
   
   </project>
   ```

1. Update `flink-connector-kinesis` to a version that is equal to or newer than 1\.15\.2\. For instance:

   ```
   <project>
   
       ...
   
       <dependencies>
   
           ...
   
           <dependency>
               <groupId>org.apache.flink</groupId>
               <artifactId>flink-connector-kinesis</artifactId>
               <version>1.15.2</version>
           </dependency>
   
           ...
   
       </dependencies>
   
       ...
   
   </project>
   ```

#### Update a Gradle dependency<a name="troubleshooting-unsupported-kinesis-connectors-update-gradle-dependency"></a>

1. Open the project’s `build.gradle` \(or `build.gradle.kts` for Kotlin applications\)\. 

1. Find the project’s dependencies\. They look like:

   ```
   ...
   
   dependencies {
   
       ...
   
       implementation("org.apache.flink:flink-connector-kinesis")
   
       ...
   
   }
   
   ...
   ```

1. Update `flink-connector-kinesis` to a version that is equal to or newer than 1\.15\.2\. For instance:

   ```
   ...
   
   dependencies {
   
       ...
   
       implementation("org.apache.flink:flink-connector-kinesis:1.15.2")
   
       ...
   
   }
   
   ...
   ```

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