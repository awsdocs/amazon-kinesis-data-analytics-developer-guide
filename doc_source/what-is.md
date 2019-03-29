# What Is Amazon Kinesis Data Analytics for SQL Applications?<a name="what-is"></a>

With Amazon Kinesis Data Analytics for SQL Applications, you can process and analyze streaming data using standard SQL\. The service enables you to quickly author and run powerful SQL code against streaming sources to perform time series analytics, feed real\-time dashboards, and create real\-time metrics\. 

To get started with Kinesis Data Analytics, you create a Kinesis data analytics application that continuously reads and processes streaming data\. The service supports ingesting data from Amazon Kinesis Data Streams and Amazon Kinesis Data Firehose streaming sources\. Then, you author your SQL code using the interactive editor and test it with live streaming data\. You can also configure destinations where you want Kinesis Data Analytics to send the results\. 

Kinesis Data Analytics supports Amazon Kinesis Data Firehose \(Amazon S3, Amazon Redshift, Amazon Elasticsearch Service, and Splunk\), AWS Lambda, and Amazon Kinesis Data Streams as destinations\.

## When Should I Use Amazon Kinesis Data Analytics?<a name="when-should-i-use"></a>

Amazon Kinesis Data Analytics enables you to quickly author SQL code that continuously reads, processes, and stores data in near real time\. Using standard SQL queries on the streaming data, you can construct applications that transform and provide insights into your data\. Following are some of example scenarios for using Kinesis Data Analytics:
+ **Generate time\-series analytics** – You can calculate metrics over time windows, and then stream values to Amazon S3 or Amazon Redshift through a Kinesis data delivery stream\.
+ **Feed real\-time dashboards** – You can send aggregated and processed streaming data results downstream to feed real\-time dashboards\.
+ **Create real\-time metrics** – You can create custom metrics and triggers for use in real\-time monitoring, notifications, and alarms\.

For information about the SQL language elements that are supported by Kinesis Data Analytics, see [Amazon Kinesis Data Analytics SQL Reference](https://docs.aws.amazon.com/kinesisanalytics/latest/sqlref/analytics-sql-reference.html)\.

## Are You a First\-Time User of Amazon Kinesis Data Analytics?<a name="first-time-user"></a>

If you are a first\-time user of Amazon Kinesis Data Analytics, we recommend that you read the following sections in order:

1. **Read the How It Works section of this guide\.** This section introduces various Kinesis Data Analytics components that you work with to create an end\-to\-end experience\. For more information, see [Amazon Kinesis Data Analytics for SQL Applications: How It Works](how-it-works.md)\.

1. **Try the Getting Started exercises\.** For more information, see [Getting Started with Amazon Kinesis Data Analytics for SQL Applications](getting-started.md)\.

1. **Explore the streaming SQL concepts\.** For more information, see [Streaming SQL Concepts](streaming-sql-concepts.md)\.

1. **Try additional examples\.** For more information, see [Example Applications](examples.md)\.