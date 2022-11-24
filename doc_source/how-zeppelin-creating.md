# Creating a Studio notebook<a name="how-zeppelin-creating"></a>

A Studio notebook contains queries or programs written in SQL, Python, or Scala that runs on streaming data and returns analytic results\. You create your application using either the console or the CLI, and provide queries for analyzing the data from your data source\.

Your application has the following components:
+ A data source, such as an Amazon MSK cluster, a Kinesis data stream, or an Amazon S3 bucket\.
+ An AWS Glue database\. This database contains tables, which store your data source and destination schemas and endpoints\. For more information, see [Working with AWS Glue](how-zeppelin-glue.md)\.
+ Your application code\. Your code implements your analytics query or program\.
+ Your application settings and runtime properties\. For information about application settings and runtime properties, see the following topics in the [Developer Guide for Apache Flink Applications](https://docs.aws.amazon.com/kinesisanalytics/latest/java/what-is.html):
  + **Application Parallelism and Scaling: ** You use your application's Parallelism setting to control the number of queries that your application can execute simultaneously\. Your queries can also take advantage of increased parallelism if they have multiple paths of execution, such as in the following circumstances:
    + When processing multiple shards of a Kinesis data stream
    + When partitioning data using the `KeyBy` operator\.
    + When using multiple window operators

    For more information about application scaling, see [ Application Scaling in Kinesis Data Analytics for Apache Flink](https://docs.aws.amazon.com/kinesisanalytics/latest/java/how-scaling.html)\.
  + **Logging and Monitoring: ** For information about application logging and monitoring, see [ Logging and Monitoring in Amazon Kinesis Data Analytics for Apache Flink](https://docs.aws.amazon.com/kinesisanalytics/latest/java/monitoring-overview.html)\.
  + Your application uses checkpoints and savepoints for fault tolerance\. Checkpoints and savepoints are not enabled by default for Studio notebooks\.

You can create your Studio notebook using either the AWS Management Console or the AWS CLI\. 

When creating the application from the console, you have the following options:
+ In the Amazon MSK console choose your cluster, then choose **Process data in real time**\.
+ In the Kinesis Data Streams console choose your data stream, then on the **Applications** tab choose **Process data in real time**\.
+ In the Kinesis Data Analytics console choose the **Studio** tab, then choose **Create Studio notebook**\.

For a tutorial about how to create a Studio notebook using either the AWS Management Console or the AWS CLI, see [Tutorial: Creating a Studio notebook in Kinesis Data Analytics](example-notebook.md)\.

For an example of a more advanced Studio notebook solution, see [Apache Flink on Amazon Kinesis Data Analytics Studio](https://streaming-analytics.workshop.aws/flink-on-kda-studio/)\.