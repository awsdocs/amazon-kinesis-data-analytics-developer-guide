# Using a Studio notebook with Kinesis Data Analytics for Apache Flink<a name="how-notebook"></a>

Studio notebooks for Kinesis Data Analytics allows you to interactively query data streams in real time, and easily build and run stream processing applications using standard SQL, Python, and Scala\. With a few clicks in the AWS Management console, you can launch a serverless notebook to query data streams and get results in seconds\. 

A notebook is a web\-based development environment\. With notebooks, you get a simple interactive development experience combined with the advanced capabilities provided by Apache Flink\. Studio notebooks uses notebooks powered by [Apache Zeppelin](https://zeppelin.apache.org/), and uses [Apache Flink](https://flink.apache.org/) as the stream processing engine\. Studio notebooks seamlessly combines these technologies to make advanced analytics on data streams accessible to developers of all skill sets\. 

Apache Zeppelin provides your Studio notebooks with a complete suite of analytics tools, including the following:
+ Data Visualization
+ Exporting data to files
+ Controlling the output format for easier analysis

To get started using Kinesis Data Analytics and Apache Zeppelin, see [Creating a Studio notebook Tutorial](example-notebook.md)\. For more information about Apache Zeppelin, see the [Apache Zeppelin documentation](http://zeppelin.apache.org)\.

 With a notebook, you model queries using the Apache Flink [ Table API & SQL](https://ci.apache.org/projects/flink/flink-docs-release-1.11/dev/table/) in SQL, Python, or Scala, or [DataStream API](https://ci.apache.org/projects/flink/flink-docs-release-1.11/dev/datastream_api.html) in Scala\. With a few clicks, you can then promote the Studio notebook to a continuously\-running, non\-interactive, Kinesis Data Analytics stream\-processing application for your production workloads\.

**Topics**
+ [Creating a Studio notebook](how-zeppelin-creating.md)
+ [Interactive analysis of streaming data](how-zeppelin-interactive.md)
+ [Deploying as an application with durable state](how-notebook-durable.md)
+ [IAM Permissions](how-zeppelin-iam.md)
+ [Connectors and dependencies](how-zeppelin-connectors.md)
+ [User\-Defined Functions](how-zeppelin-udf.md)
+ [Enabling Checkpointing](how-zeppelin-checkpoint.md)
+ [Working with AWS Glue](how-zeppelin-glue.md)
+ [Examples and Tutorials](how-zeppelin-examples.md)
+ [Studio Notebooks vs\. SQL Applications](how-zeppelin-kdasql.md)
+ [Troubleshooting](how-zeppelin-troubleshooting.md)
+ [Appendix: Creating custom IAM policies](how-zeppelin-appendix-iam.md)