# Kinesis Data Analytics for Apache Flink: How It Works<a name="how-it-works"></a>

Kinesis Data Analytics for Apache Flink is a fully managed Amazon service that enables you to use an Apache Flink application to process streaming data\. 

## Programming Your Apache Flink Application<a name="how-it-works-programming"></a>

An Apache Flink application is a Java or Scala application that is created with the Apache Flink framework\. You author and build your Apache Flink application locally\. 

Applications primarily use either the [DataStream API](https://nightlies.apache.org/flink/flink-docs-release-1.15/dev/datastream_api.html) or the [ Table API](https://nightlies.apache.org/flink/flink-docs-release-1.15/dev/table/)\. The other Apache Flink APIs are also available for you to use, but they are less commonly used in building streaming applications\.

The features of the two APIs are as follows:

### DataStream API<a name="how-it-works-prog-datastream"></a>

The Apache Flink DataStream API programming model is based on two components:
+ **Data stream:** The structured representation of a continuous flow of data records\.
+ **Transformation operator:** Takes one or more data streams as input, and produces one or more data streams as output\.

Applications created with the DataStream API do the following:
+ Read data from a Data Source \(such as a Kinesis stream or Amazon MSK topic\)\.
+ Apply transformations to the data, such as filtering, aggregation, or enrichment\.
+ Write the transformed data to a Data Sink\.

Applications that use the DataStream API can be written in Java or Scala, and can read from a Kinesis data stream, a Amazon MSK topic, or a custom source\.

Your application processes data by using a *connector*\. Apache Flink uses the following types of connectors: 
+ **Source**: A connector used to read external data\.
+ **Sink**: A connector used to write to external locations\. 
+ **Operator**: A connector used to process data within the application\.

A typical application consists of at least one data stream with a source, a data stream with one or more operators, and at least one data sink\.

For more information about using the DataStream API, see [DataStream API](how-datastream.md)\.

### Table API<a name="how-it-works-prog-table"></a>

The Apache Flink Table API programming model is based on the following components:
+ **Table Environment:** An interface to underlying data that you use to create and host one or more tables\. 
+ **Table:** An object providing access to a SQL table or view\.
+ **Table Source:** Used to read data from an external source, such as an Amazon MSK topic\.
+ **Table Function:** A SQL query or API call used to transform data\.
+ **Table Sink:** Used to write data to an external location, such as an Amazon S3 bucket\.

Applications created with the Table API do the following:
+ Create a `TableEnvironment` by connecting to a `Table Source`\. 
+ Create a table in the `TableEnvironment` using either SQL queries or Table API functions\.
+ Run a query on the table using either Table API or SQL
+ Apply transformations on the results of the query using Table Functions or SQL queries\.
+ Write the query or function results to a `Table Sink`\.

Applications that use the Table API can be written in Java or Scala, and can query data using either API calls or SQL queries\. 

For more information about using the Table API, see [Table API](how-table.md)\.

## Creating Your Kinesis Data Analytics Application<a name="how-it-works-app"></a>

Kinesis Data Analytics application is an AWS resource that is hosted by the Kinesis Data Analytics service\. Your Kinesis Data Analytics application hosts your Apache Flink application and provides it with the following settings:
+ **[Runtime Properties](how-properties.md): ** Parameters that you can provide to your application\. You can change these parameters without recompiling your application code\.
+ **[Fault Tolerance](how-fault.md)**: How your application recovers from interrupts and restarts\.
+ **[Logging and Monitoring](monitoring-overview.md)**: How your application logs events to CloudWatch Logs\. 
+ **[ScalingProduction readiness](how-scaling.md)**: How your application provisions computing resources\.

You create your Kinesis Data Analytics application using either the console or the AWS CLI\. To get started creating a Kinesis Data Analytics application, see [Getting Started \(DataStream API\)](getting-started.md)\.