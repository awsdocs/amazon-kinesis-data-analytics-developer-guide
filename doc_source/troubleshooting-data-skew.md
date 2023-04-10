# Data skew<a name="troubleshooting-data-skew"></a>

A Flink application is executed on a cluster in a distributed fashion\. To scale out to multiple nodes, Flink uses the concept of keyed streams, which essentially means that the events of a stream are partitioned according to a specific key, e\.g\., customer id, and Flink can then process different partitions on different nodes\. Many of the Flink operators are then evaluated based on these partitions, e\.g\., [Keyed Windows](https://nightlies.apache.org/flink/flink-docs-stable/docs/dev/datastream/operators/windows/), [Process Functions](https://nightlies.apache.org/flink/flink-docs-stable/docs/dev/datastream/operators/process_function/) and [Async I/O](https://nightlies.apache.org/flink/flink-docs-stable/docs/dev/datastream/operators/asyncio/)\.

Choosing a partition key often depends on the business logic\. At the same time, many of the best practices for, e\.g\., [DynamoDB](https://aws.amazon.com/dynamodb/) and Spark, equally apply to Flink, including:
+ ensuring a high cardinality of partition keys
+ avoiding skew in the event volume between partitions

 You can identify skew in the partitions by comparing the records received/sent of subtasks \(i\.e\., instances of the same operator\) in the Flink dashboard\. In addition, Kinesis Data Analytics monitoring can be configured to expose metrics for `numRecordsIn/Out` and `numRecordsInPerSecond/OutPerSecond` on a subtask level as well\.