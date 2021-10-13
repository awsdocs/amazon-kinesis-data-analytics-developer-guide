# Getting Started with Amazon Kinesis Data Analytics for Apache Flink \(DataStream API\)<a name="getting-started"></a>

This section introduces you to the fundamental concepts of Kinesis Data Analytics for Apache Flink and the DataStream API\. It describes the available options for creating and testing your applications\. It also provides instructions for installing the necessary tools to complete the tutorials in this guide and to create your first application\. 

**Topics**
+ [Components of a Kinesis Data Analytics for Flink Application](#getting-started-components)
+ [Prerequisites for Completing the Exercises](#setting-up-prerequisites)
+ [Step 1: Set Up an AWS Account and Create an Administrator User](setting-up.md)
+ [Step 2: Set Up the AWS Command Line Interface \(AWS CLI\)](setup-awscli.md)
+ [Step 3: Create and Run a Kinesis Data Analytics for Apache Flink Application](get-started-exercise.md)
+ [Step 4: Clean Up AWS Resources](getting-started-cleanup.md)
+ [Step 5: Next Steps](getting-started-next-steps.md)

## Components of a Kinesis Data Analytics for Flink Application<a name="getting-started-components"></a>

To process data, your Kinesis Data Analytics application uses a Java/Apache Maven or Scala application that processes input and produces output using the Apache Flink runtime\. 

A Kinesis Data Analytics application has the following components:
+ **Runtime properties:** You can use *runtime properties* to configure your application without recompiling your application code\. 
+ **Source:** The application consumes data by using a *source*\. A source connector reads data from a Kinesis data stream, an Amazon S3 bucket, etc\. For more information, see [Sources](how-sources.md)\.
+ **Operators:** The application processes data by using one or more *operators*\. An operator can transform, enrich, or aggregate data\. For more information, see [DataStream API Operators](how-operators.md)\.
+ **Sink:** The application produces data to external sources by using *sinks*\. A sink connector writes data to a Kinesis data stream, a Kinesis Data Firehose delivery stream, an Amazon S3 bucket, etc\. For more information, see [Sinks](how-sinks.md)\.

After you create, compile, and package your application code, you upload the code package to an Amazon Simple Storage Service \(Amazon S3\) bucket\. You then create a Kinesis Data Analytics application\. You pass in the code package location, a Kinesis data stream as the streaming data source, and typically a streaming or file location that receives the application's processed data\.

## Prerequisites for Completing the Exercises<a name="setting-up-prerequisites"></a>

To complete the steps in this guide, you must have the following:
+ [Java Development Kit \(JDK\) version 11](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html)\. Set the `JAVA_HOME` environment variable to point to your JDK install location\.
+ We recommend that you use a development environment \(such as [Eclipse Java Neon](http://www.eclipse.org/downloads/packages/release/neon/3) or [IntelliJ Idea](https://www.jetbrains.com/idea/)\) to develop and compile your application\.
+ [Git client](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)\. Install the Git client if you haven't already\.
+ [Apache Maven Compiler Plugin](https://maven.apache.org/plugins/maven-compiler-plugin/)\. Maven must be in your working path\. To test your Apache Maven installation, enter the following:

  ```
  $ mvn -version
  ```

To get started, go to [Step 1: Set Up an AWS Account and Create an Administrator User](setting-up.md)\.