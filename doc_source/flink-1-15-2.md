# Amazon Kinesis Data Analytics for Apache Flink 1\.15\.2 release<a name="flink-1-15-2"></a>

 Kinesis Data Analytics supports the following new features in Apache 1\.15\.2 


****  

| Feature | Description | Apache FLIP reference  | 
| --- | --- | --- | 
| Async Sink  | An AWS contributed framework for building async destinations that allows developers to build custom AWS connectors with less than half the previous effort\. For more information, see [The Generic Asynchronous Base Sink](https://flink.apache.org/2022/05/06/async-sink-base.html)\. | [FLIP\-171: Async Sink](https://cwiki.apache.org/confluence/display/FLINK/FLIP-171%3A+Async+Sink)\. | 
| Kinesis Data Firehose Sink  | AWS has contributed a new Amazon Kinesis Firehose Sink using the Async framework\.  | [Amazon Kinesis Data Firehose Sink](https://nightlies.apache.org/flink/flink-docs-release-1.15/docs/connectors/datastream/firehose/)\. | 
| Stop with Savepoint  | Stop with Savepoint ensures a clean stop operation, most importantly supporting exactly\-once semantics for customers that rely on them\. | [FLIP\-34: Terminate/Suspend Job with Savepoint](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=103090212)\. | 
| Scala Decoupling  | Users can now leverage the Java API from any Scala version, including Scala 3\. Customers will need to bundle the Scala standard library of their choice in their Scala applications\.  | [FLIP\-28: Long\-term goal of making flink\-table Scala\-free](https://cwiki.apache.org/confluence/display/FLINK/FLIP-28%3A+Long-term+goal+of+making+flink-table+Scala-free)\. | 
| Unified Connector Metrics | Flink has [defined standard metrics](https://nightlies.apache.org/flink/flink-docs-release-1.15/docs/ops/metrics/) for jobs, tasks and operators\. Kinesis Data Analytics will continue to support sink and source metrics and in 1\.15 introduce numRestarts in parallel with fullRestarts for Availability Metrics\.  | [FLIP\-33: Standardize Connector Metrics](https://cwiki.apache.org/confluence/display/FLINK/FLIP-33%3A+Standardize+Connector+Metrics) and [FLIP\-179: Expose Standardized Operator Metrics](https://cwiki.apache.org/confluence/display/FLINK/FLIP-179%3A+Expose+Standardized+Operator+Metrics)\. | 
| Checkpointing finished tasks  | This feature is enabled by default in Flink 1\.15 and makes it possible to continue performing checkpoints even if parts of the job graph have finished processing all data, which might happen if it contains bounded \(batch\) sources\.  | [FLIP\-147: Support Checkpoints After Tasks Finished](https://cwiki.apache.org/confluence/display/FLINK/FLIP-147%3A+Support+Checkpoints+After+Tasks+Finished)\.  | 

## Changes in Amazon Kinesis Data Analytics with Apache Flink 1\.15<a name="flink-1-15-2-known-issues"></a>

**Kinesis connectors**
+ Kinesis Data Analytics for Apache Flink version 1\.15 will automatically prevent applications from starting or updating if they are using unsupported Kinesis Connector versions \(Bundled into application JARs\)\. When upgrading to Kinesis Data Analytics for Apache Flink version 1\.15 please ensure that you are using the most recent Kinesis Connector\. 
+ This is for any version 1\.15\.2 or newer\. All other versions will not be supported by Kinesis Data Analytics for Apache Flink as they may cause consistency issues or failures with the `Stop with Savepoint` feature preventing clean [stop/update](https://issues.apache.org/jira/browse/FLINK-23528) operations\. 

**Kinesis Data Firehose Sink **

When upgrading to Kinesis Data Analytics for Apache Flink version 1\.15 please ensure that you are using the most recent [Amazon Kinesis Data Firehose Sink](https://nightlies.apache.org/flink/flink-docs-release-1.15/docs/connectors/datastream/firehose/)\.

**Scala Decoupling **

Starting with Flink 1\.15\.2, you will need to bundle the Scala standard library of your choice in your Scala applications\. 

**Kafka Connectors **

When upgrading to Amazon Kinesis Data Analytics for Apache Flink version 1\.15, ensure that you are using the most recent Kafka connector APIs\. Apache Flink has deprecated [FlinkKafkaConsumer](https://nightlies.apache.org/flink/flink-docs-master/docs/connectors/datastream/kafka/#kafka-consumer) and [FlinkKafkaProducer](https://nightlies.apache.org/flink/flink-docs-master/docs/connectors/datastream/kafka/#kafka-producer) These APIs for the Kafka sink cannot commit to Kafka for Flink 1\.15\. Ensure you are using [KafkaSource](https://nightlies.apache.org/flink/flink-docs-master/docs/connectors/datastream/kafka/#kafka-source) and [KafkaSink](https://nightlies.apache.org/flink/flink-docs-master/docs/connectors/datastream/kafka/#kafka-sink)\. 

## Components<a name="flink-1-15-2-components"></a>


****  

| Component | Version | 
| --- | --- | 
| Java  | 11 \(recommended\) | 
| Scala | 2\.12 | 
| Kinesis Data Analytics Flink Runtime \(aws\-kinesisanalytics\-runtime\) | 1\.2\.0 | 
| [AWS Kinesis Connector \(flink\-connector\-kinesis\)](https://mvnrepository.com/artifact/org.apache.flink/flink-connector-kinesis/1.15.2) | 1\.15\.2 | 
| [Apache Beam \(Beam applications only\)](https://aws.amazon.com/developer/language/python/) | 2\.33\.0, with Jackson version 2\.12\.2 | 

## Known issues<a name="flink-1-15-2-known-issues"></a>

**EFO Connector**

Customer applications using the EFO connector may experience infrequent intermittent failures when updating their applications\. In such cases applications will be required to be restarted using the most recent savepoint\.

**Application update times**

Application updates in Flink 1\.15 may be increased by between 1 and 3 minutes when compared to Flink 1\.13\. We are working to resolve this within the next 6 weeks\. 

**Async Sink Performance**

There is a known degradation in the 1\.15 AsyncSink performance under high load scenarios compared to the legacy sink, specifically with high number of shards \(64 or more\)\. Other influential factors are larger payload sizes and higher parallelism apps\.

**Kinesis Data Analytics Studio**

Studio utilizes Apache Zeppelin notebooks to provide a single\-interface development experience for developing, debugging code, and running Apache Flink stream processing applications\. An upgrade is required to Zeppelin’s Flink Interpreter to enable support of Flink 1\.15\. This work is scheduled with the Zeppelin community and we will update these notes when it is complete\. You can continue to use [Studio with Kinesis Data Analytics for Apache Flink 1\.13](https://docs.aws.amazon.com/kinesisanalytics/latest/java/how-notebook.html)\.