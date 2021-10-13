# Earlier Version Information for Kinesis Data Analytics for Apache Flink<a name="earlier"></a>

This topic contains information about using Kinesis Data Analytics with older versions of Apache Flink\. The versions of Apache Flink that Kinesis Data Analytics supports are **1\.13\.2** \(recommended\), **1\.11\.3**, **1\.11\.1**, **1\.8\.2** and **1\.6\.2**\.

We recommend that you use the latest supported version of Apache Flink with your Kinesis Data Analytics application\. Apache Flink version 1\.13\.2 has the following features:
+ Support for [ Apache Flink Table API & SQL](https://ci.apache.org/projects/flink/flink-docs-release-1.11/dev/table/)
+ Support for Python applications\.
+ Support for Java version 11 and Scala version 2\.12
+ An improved memory model
+ RocksDB optimizations for increased application stability
+ Support for task manager and stack traces in the Apache Flink Dashboard\.

**Topics**
+ [Using the Apache Flink Kinesis Streams Connector with previous Apache Flink versions](#how-creating-apps-building-kinesis)
+ [Building Applications with Apache Flink 1\.8\.2](#earlier-buildingapps-1_8)
+ [Building Applications with Apache Flink 1\.6\.2](#earlier-buildingapps-1_6)
+ [Upgrading Applications](#earlier-upgrading)
+ [Available Connectors in Apache Flink 1\.6\.2 and 1\.8\.2](#earlier-connectors)
+ [Getting Started: Flink 1\.11\.3](earlier-gs-1_11.md)
+ [Getting Started: Flink 1\.8\.2](earlier-gs-1_8.md)
+ [Getting Started: Flink 1\.6\.2](earlier-gs-1_6.md)

## Using the Apache Flink Kinesis Streams Connector with previous Apache Flink versions<a name="how-creating-apps-building-kinesis"></a>

The Apache Flink Kinesis Streams connector was not included in Apache Flink prior to version 1\.11\. In order for your application to use the Apache Flink Kinesis connector with previous versions of Apache Flink, you must download, compile, and install the version of Apache Flink that your application uses\. This connector is used to consume data from a Kinesis stream used as an application source, or to write data to a Kinesis stream used for application output\.

**Note**  
Ensure that you are building the connector with [ KPL version 0\.14\.0](https://mvnrepository.com/artifact/com.amazonaws/amazon-kinesis-producer/0.14.0) or higher\. 

To download and install the Apache Flink version 1\.8\.2 source code, do the following:

1. Ensure that you have [Apache Maven](https://maven.apache.org/) installed, and your `JAVA_HOME` environment variable points to a JDK rather than a JRE\. You can test your Apache Maven install with the following command:

   ```
   mvn -version
   ```

1. Download the Apache Flink version 1\.8\.2 source code:

   ```
   wget https://archive.apache.org/dist/flink/flink-1.8.2/flink-1.8.2-src.tgz
   ```

1. Uncompress the Apache Flink source code:

   ```
   tar -xvf flink-1.8.2-src.tgz
   ```

1. Change to the Apache Flink source code directory:

   ```
   cd flink-1.8.2
   ```

1. Compile and install Apache Flink:

   ```
   mvn clean install -Pinclude-kinesis -DskipTests
   ```
**Note**  
If you are compiling Flink on Microsoft Windows, you need to add the `-Drat.skip=true` parameter\.

## Building Applications with Apache Flink 1\.8\.2<a name="earlier-buildingapps-1_8"></a>

This section contains information about components that you use for building Kinesis Data Analytics applications that work with Apache Flink 1\.8\.2\.

Use the following component versions for Kinesis Data Analytics applications:


****  

| Component | Version | 
| --- | --- | 
| Java | 1\.8 \(recommended\) | 
| Apache Flink | 1\.8\.2 | 
| Kinesis Data Analytics for Flink Runtime \(aws\-kinesisanalytics\-runtime\) | 1\.0\.1 | 
| Kinesis Data Analytics Flink Connectors \(aws\-kinesisanalytics\-flink\) | 1\.0\.1 | 
| Apache Maven | 3\.1 | 

To compile an application using Apache Flink 1\.8\.2, run Maven with the following parameter:

```
mvn package -Dflink.version=1.8.2
```

For an example of a `pom.xml` file for a Kinesis Data Analytics application that uses Apache Flink version 1\.8\.2, see the [Kinesis Data Analytics for Flink 1\.8\.2 Getting Started Application](https://github.com/aws-samples/amazon-kinesis-data-analytics-java-examples/blob/master/GettingStarted_1_8/pom.xml)\.

For information about how to build and use application code for a Kinesis Data Analytics application, see [Creating Applications](how-creating-apps.md)\.

## Building Applications with Apache Flink 1\.6\.2<a name="earlier-buildingapps-1_6"></a>

This section contains information about components that you use for building Kinesis Data Analytics applications that work with Apache Flink 1\.6\.2\.

Use the following component versions for Kinesis Data Analytics applications:


****  

| Component | Version | 
| --- | --- | 
| Java | 1\.8 \(recommended\) | 
| AWS Java SDK | 1\.11\.379 | 
| Apache Flink | 1\.6\.2 | 
| Kinesis Data Analytics for Flink Runtime \(aws\-kinesisanalytics\-runtime\) | 1\.0\.1 | 
| Kinesis Data Analytics Flink Connectors \(aws\-kinesisanalytics\-flink\) | 1\.0\.1 | 
| Apache Maven | 3\.1 | 
| Apache Beam | Not supported with Apache Flink 1\.6\.2\. | 

**Note**  
When using Kinesis Data Analytics Runtime version **1\.0\.1**, you specify the version of Apache Flink in your `pom.xml` file rather than using the `-Dflink.version` parameter when compiling your application code\.

For an example of a `pom.xml` file for a Kinesis Data Analytics application that uses Apache Flink version 1\.6\.2, see the [Kinesis Data Analytics for Flink 1\.6\.2 Getting Started Application](https://github.com/aws-samples/amazon-kinesis-data-analytics-java-examples/blob/master/GettingStarted_1_6/pom.xml)\.

For information about how to build and use application code for a Kinesis Data Analytics application, see [Creating Applications](how-creating-apps.md)\.

## Upgrading Applications<a name="earlier-upgrading"></a>

To upgrade the version of an Kinesis Data Analytics application, you must update your application code, delete the previous application, and create a new application with the updated code\. To do this, do the following:
+ Change the versions of the Kinesis Data Analytics Runtime and Kinesis Data Analytics Flink connectors \(aws\-kinesisanalytics\-flink\) in your application's `pom.xml` file to 1\.1\.0\.
+ Remove the `flink.version` property from your application's `pom.xml` file\. You will provide this parameter when you compile the application code in the next step\.
+ Recompile your application code using the following command:

  ```
  mvn package -Dflink.version=1.13.2
  ```
+ Delete your existing application\. Create your application again, and choose **Apache Flink version 1\.13\.2 \(Recommended version\)** for the application's **Runtime**\.

**Note**  
You cannot use snapshots from your previous application versions\.

## Available Connectors in Apache Flink 1\.6\.2 and 1\.8\.2<a name="earlier-connectors"></a>

The Apache Flink framework contains connectors for accessing data from a variety of sources\. 
+ For information about connectors available in the Apache Flink 1\.6\.2 framework, see [Connectors \(1\.6\.2\)](https://ci.apache.org/projects/flink/flink-docs-release-1.6/dev/connectors/) in the [Apache Flink documentation \(1\.6\.2\)](https://ci.apache.org/projects/flink/flink-docs-release-1.6/)\.
+ For information about connectors available in the Apache Flink 1\.8\.2 framework, see [Connectors \(1\.8\.2\)](https://ci.apache.org/projects/flink/flink-docs-release-1.8/dev/connectors/) in the [Apache Flink documentation \(1\.8\.2\)](https://ci.apache.org/projects/flink/flink-docs-release-1.8/)\.