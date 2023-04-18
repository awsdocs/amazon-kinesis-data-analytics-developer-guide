# Monitoring<a name="monitoring"></a>

When running streaming applications in production, you set out to execute the application continuously and indefinitely\. It is crucial to implement monitoring and proper alarming of all components not only the Flink application\. Otherwise you risk to miss emerging problems early on and only realize an operational event once it is fully unravelling and much harder to mitigate\. General things to monitor include:
+ Is the source ingesting data?
+ Is data read from the source \(from the perspective of the source\)?
+ Is the Flink application receiving data?
+ Is the Flink application able to keep up or is it falling behind?
+ Is the Flink application persisting data into the sink \(from the application perspective\)?
+ Is the sink receiving data?

More specific metrics should then be considered for the Flink application\. This [CloudWatch dashboard](https://github.com/aws-samples/kda-metrics-dashboard) provides a good starting point\. For more information on what metrics to monitor for production applications, see [Using CloudWatch Alarms with Amazon Kinesis Data Analytics for Apache Flink](monitoring-metrics-alarms.md)\. These metrics include:
+ **records\_lag\_max** and **millisBehindLatest** – If the application is consuming from Kinesis or Kafka, these metrics indicate if the application is falling behind and needs to be scaled in order to keep up with the current load\. This is a good generic metric that is easy to track for all kinds of applications\. But it can only be used for reactive scaling, i\.e\., when the application has already fallen behind\.
+ **cpuUtilization** and **heapMemoryUtilization** – These metrics give a good indication of the overall resource utilization of the application and can be used for proactive scaling unless the application is I/O bound\.
+ **downtime** – A downtime greater than zero indicates that the application has failed\. If the value is larger than 0, the application is not processing any data\.
+ **lastCheckpointSize** and *lastCheckpointDuration* – These metrics monitor how much data is stored in state and how long it takes to take a checkpoint\. If checkpoints grow or take long, the application is continuously spending time on checkpointing and has less cycles for actual processing\. At some points, checkpoints may grow so or take so long that they fail\. In addition to monitoring absolute values, customers should also considering monitoring the change rate with `RATE(lastCheckpointSize)` and `RATE(lastCheckpointDuration)`\.
+ **numberOfFailedCheckpoints** – This metric counts the number of failed checkpoints since the application started\. Depending on the application, it can be tolerable if checkpoints fail occasionally\. But if checkpoints are regularly failing, the application is likely unhealthy and needs further attention\. We recommend monitoring `RATE(numberOfFailedCheckpoints)` to alarm on the gradient and not on absolute values\.