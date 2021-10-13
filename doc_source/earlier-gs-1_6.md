# Getting Started: Flink 1\.6\.2<a name="earlier-gs-1_6"></a>

This topic contains a version of the [Getting Started \(DataStream API\)](getting-started.md) Tutorial that uses Apache Flink 1\.6\.2\.

**Topics**
+ [Components of a Kinesis Data Analytics for Apache Flink application](#earlier-gs-1_6-components)
+ [Prerequisites for Completing the Exercises](#su-1_6-prerequisites)
+ [Step 1: Set Up an AWS Account and Create an Administrator User](su-1_6.md)
+ [Step 2: Set Up the AWS Command Line Interface \(AWS CLI\)](su-1_6-awscli.md)
+ [Step 3: Create and Run a Kinesis Data Analytics application](earlier-gs-1_6-exercise.md)
+ [Step 4: Clean Up AWS Resources](earlier-gs-1_6-cleanup.md)

## Components of a Kinesis Data Analytics for Apache Flink application<a name="earlier-gs-1_6-components"></a>

To process data, your Kinesis Data Analytics application uses a Java/Apache Maven or Scala application that processes input and produces output using the Apache Flink runtime\. 

A Kinesis Data Analytics for Apache Flink application has the following components:
+ **Runtime properties:** You can use *runtime properties* to configure your application without recompiling your application code\. 
+ **Source:** The application consumes data by using a *source*\. A source connector reads data from a Kinesis data stream, an Amazon S3 bucket, etc\. For more information, see [Sources](how-sources.md)\.
+ **Operators:** The application processes data by using one or more *operators*\. An operator can transform, enrich, or aggregate data\. For more information, see [DataStream API Operators](how-operators.md)\.
+ **Sink:** The application produces data to external sources by using *sinks*\. A sink connector writes data to a Kinesis data stream, a Kinesis Data Firehose delivery stream, an Amazon S3 bucket, etc\. For more information, see [Sinks](how-sinks.md)\.

After you create, compile, and package your application, you upload the code package to an Amazon Simple Storage Service \(Amazon S3\) bucket\. You then create a Kinesis Data Analytics application\. You pass in the code package location, a Kinesis data stream as the streaming data source, and typically a streaming or file location that receives the application's processed data\.

## Prerequisites for Completing the Exercises<a name="su-1_6-prerequisites"></a>

To complete the steps in this guide, you must have the following:
+ [Java Development Kit](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) \(JDK\) version 8\. Set the `JAVA_HOME` environment variable to point to your JDK install location\.
+ We recommend that you use a development environment \(such as [Eclipse Java Neon](http://www.eclipse.org/downloads/packages/release/neon/3) or [IntelliJ Idea](https://www.jetbrains.com/idea/)\) to develop and compile your application\.
+ [Git Client\.](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git) Install the Git client if you haven't already\.
+ [Apache Maven Compiler Plugin](https://maven.apache.org/plugins/maven-compiler-plugin/)\. Maven must be in your working path\. To test your Apache Maven installation, enter the following:

  ```
  $ mvn -version
  ```

To get started, go to [Step 1: Set Up an AWS Account and Create an Administrator User](su-1_6.md)\.