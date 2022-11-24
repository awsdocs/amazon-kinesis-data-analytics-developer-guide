# Viewing Kinesis Data Analytics Metrics and Dimensions<a name="metrics-dimensions"></a>

**Topics**
+ [Application Metrics](#metrics-dimensions-jobs)
+ [Kinesis Data Streams Connector Metrics](#metrics-dimensions-stream)
+ [Amazon MSK Connector Metrics](#metrics-dimensions-msk)
+ [Apache Zeppelin Metrics](#metrics-dimensions-zeppelin)
+ [Viewing CloudWatch Metrics](#metrics-dimensions-viewing)
+ [Setting CloudWatch Metrics Reporting Levels](#cloudwatch-logs-levels)
+ [Using Custom Metrics with Amazon Kinesis Data Analytics for Apache Flink](monitoring-metrics-custom.md)
+ [Using CloudWatch Alarms with Amazon Kinesis Data Analytics for Apache Flink](monitoring-metrics-alarms.md)

When your Amazon Kinesis Data Analytics for Apache Flink processes a data source, Kinesis Data Analytics reports the following metrics and dimensions to Amazon CloudWatch\.

## Application Metrics<a name="metrics-dimensions-jobs"></a>


| Metric | Unit | Description | Level | Usage Notes | 
| --- | --- | --- | --- | --- | 
| backPressuredTimeMsPerSecond\* | Milliseconds | The time \(in milliseconds\) this task or operator is back pressured per second\. | Task, Operator, Parallelism | \*Available for KDA applications running Flink version 1\.13 only\. These metrics can be useful in identifying bottlenecks in an application\. | 
| busyTimeMsPerSecond\* | Milliseconds | The time \(in milliseconds\) this task or operator is busy \(neither idle nor back pressured\) per second\. Can be NaN, if the value could not be calculated\. | Task, Operator, Parallelism | \*Available for KDA applications running Flink version 1\.13 only\. These metrics can be useful in identifying bottlenecks in an application\. | 
| cpuUtilization | Percentage | Overall percentage of CPU utilization across task managers\. For example, if there are five task managers, Kinesis Data Analytics publishes five samples of this metric per reporting interval\. | Application | You can use this metric to monitor minimum, average, and maximum CPU utilization in your application\. The CPUUtilization metric only accounts for CPU usage of the TaskManager JVM process running inside the container\.  | 
| containerCPUUtilization | Percentage | Overall percentage of CPU utilization across task manager containers in Flink application cluster\. For example, if there are five task managers, correspondingly there are five TaskManager containers and Kinesis Data Analytics publishes 2 \* five samples of this metric per 1 minute reporting interval\. | Application | It is calculated per container as: *Total CPU time \(in seconds\) consumed by container \* 100 / Container CPU limit \(in CPUs/seconds\)* The `CPUUtilization` metric only accounts for CPU usage of the TaskManager JVM process running inside the container\. There are other components running outside the JVM within the same container\. The `containerCPUUtilization` metric gives you a more complete picture, including all processes in terms of CPU exhaustion at the container and failures resulting from that\.  | 
| containerMemoryUtilization | Percentage | Overall percentage of memory utilization across task manager containers in Flink application cluster\. For example, if there are five task managers, correspondingly there are five TaskManager containers and Kinesis Data Analytics publishes 2 \* five samples of this metric per 1 minute reporting interval\. | Application | It is calculated per container as: *Container memory usage \(bytes\) \* 100 / Container memory limit as per pod deployment spec \(in bytes\)* The `HeapMemoryUtilization` and `ManagedMemoryUtilzations` metrics only account for specific memory metrics like Heap Memory Usage of TaskManager JVM or Managed Memory \(memory usage outside JVM for native processes like [RocksDB State Backend](https://flink.apache.org/2021/01/18/rocksdb.html#:~:text=Conclusion-,The%20RocksDB%20state%20backend%20(i.e.%2C%20RocksDBStateBackend)%20is%20one%20of,with%20exactly%2Donce%20processing%20guarantees.)\)\. The `containerMemoryUtilization` metric gives you a more complete picture by including the working set memory, which is a better tracker of total memory exhaustion\. Upon its exhaustion, it will result in `Out of Memory Error` for the TaskManager pod\.  | 
| containerDiskUtilization | Percentage | Overall percentage of disk utilization across task manager containers in Flink application cluster\. For example, if there are five task managers, correspondingly there are five TaskManager containers and Kinesis Data Analytics publishes 2 \* five samples of this metric per 1 minute reporting interval\. | Application | It is calculated per container as: *Disk usage in bytes \* 100 / Disk Limit for container in bytes* For containers, it represents utilization of the filesystem on which root volume of the container is set up\.  | 
| currentInputWatermark | Milliseconds | The last watermark this application/operator/task/thread has received | Application, Operator, Task, Parallelism | This record is only emitted for dimensions with two inputs\. This is the minimum value of the last received watermarks\. | 
| currentOutputWatermark | Milliseconds | The last watermark this application/operator/task/thread has emitted | Application, Operator, Task, Parallelism |  | 
| downtime | Milliseconds | For jobs currently in a failing/recovering situation, the time elapsed during this outage\. | Application | This metric measures the time elapsed while a job is failing or recovering\. This metric returns 0 for running jobs and \-1 for completed jobs\. If this metric is not 0 or \-1, this indicates that the Apache Flink job for the application failed to run\.  | 
| fullRestarts | Count | The total number of times this job has fully restarted since it was submitted\. This metric does not measure fine\-grained restarts\. | Application | You can use this metric to evaluate general application health\. Restarts can occur during internal maintenance by Kinesis Data Analytics\. Restarts higher than normal can indicate a problem with the application\. | 
| heapMemoryUtilization | Percentage | Overall heap memory utilization across task managers\. For example, if there are five task managers, Kinesis Data Analytics publishes five samples of this metric per reporting interval\. | Application | You can use this metric to monitor minimum, average, and maximum heap memory utilization in your application\. The HeapMemoryUtilization only accounts for specific memory metrics like Heap Memory Usage of TaskManager JVM\. | 
| idleTimeMsPerSecond\* | Milliseconds | The time \(in milliseconds\) this task or operator is idle \(has no data to process\) per second\. Idle time excludes back pressured time, so if the task is back pressured it is not idle\. | Task, Operator, Parallelism | \*Available for KDA applications running Flink version 1\.13 only\. These metrics can be useful in identifying bottlenecks in an application\. | 
| lastCheckpointSize | Bytes | The total size of the last checkpoint | Application | You can use this metric to determine running application storage utilization\. If this metric is increasing in value, this may indicate that there is an issue with your application, such as a memory leak or bottleneck\. | 
| lastCheckpointDuration | Milliseconds | The time it took to complete the last checkpoint | Application | This metric measures the time it took to complete the most recent checkpoint\. If this metric is increasing in value, this may indicate that there is an issue with your application, such as a memory leak or bottleneck\. In some cases, you can troubleshoot this issue by disabling checkpointing\. | 
| managedMemoryUsed\* | Bytes | The amount of managed memory currently used\. | Application, Operator, Task, Parallelism | \*Available for KDA applications running Flink version 1\.13 only\. This relates to memory managed by Flink outside the Java heap\. It is used for the RocksDB state backend, and is also available to applications\. | 
| managedMemoryTotal\* | Bytes | The total amount of managed memory\. | Application, Operator, Task, Parallelism | \*Available for KDA applications running Flink version 1\.13 only\. This relates to memory managed by Flink outside the Java heap\. It is used for the RocksDB state backend, and is also available to applications\. The `ManagedMemoryUtilzations` metric only accounts for specific memory metrics like Managed Memory \(memory usage outside JVM for native processes like [RocksDB State Backend](https://flink.apache.org/2021/01/18/rocksdb.html#:~:text=Conclusion-,The%20RocksDB%20state%20backend%20(i.e.%2C%20RocksDBStateBackend)%20is%20one%20of,with%20exactly%2Donce%20processing%20guarantees.)\) | 
| managedMemoryUtilization\* | Percentage | Derived by managedMemoryUsed/managedMemoryTotal | Application, Operator, Task, Parallelism | \*Available for KDA applications running Flink version 1\.13 only\. This relates to memory managed by Flink outside the Java heap\. It is used for the RocksDB state backend, and is also available to applications\. | 
| numberOfFailedCheckpoints | Count | The number of times checkpointing has failed\. | Application | You can use this metric to monitor application health and progress\. Checkpoints may fail due to application problems, such as throughput or permissions issues\.  | 
| numRecordsIn | Count | The total number of records this application, operator, or task has received\. | Application, Operator, Task, Parallelism | The metric's Level specifies whether this metric measures the total number of records the entire application, a specific operator, or a specific task has received\. | 
| numRecordsInPerSecond | Count/Second | The total number of records this application, operator or task has received per second\. | Application, Operator, Task, Parallelism | The metric's Level specifies whether this metric measures the total number of records the entire application, a specific operator, or a specific task has received per second\. | 
| numRecordsOut | Count | The total number of records this application, operator or task has emitted\. | Application, Operator, Task, Parallelism | The metric's Level specifies whether this metric measures the total number of records the entire application, a specific operator, or a specific task has emitted\. | 
| numLateRecordsDropped | Count | The number of records this operator or task has dropped due to arriving late\. | Application, Operator, Task, Parallelism |  | 
| numRecordsOutPerSecond | Count/Second | The total number of records this application, operator or task has emitted per second\. | Application, Operator, Task, Parallelism | The metric's Level specifies whether this metric measures the total number of records the entire application, a specific operator, or a specific task has emitted per second\. | 
| oldGenerationGCCount | Count | The total number of old garbage collection operations that have occurred across all task managers\.  | Application |  | 
| oldGenerationGCTime | Milliseconds | The total time spent performing old garbage collection operations\.  | Application | You can use this metric to monitor sum, average, and maximum garbage collection time\. | 
| threadCount | Count | The total number of live threads used by the application\.  | Application | This metric measures the number of threads used by the application code\. This is not the same as application parallelism\. | 
| uptime | Milliseconds | The time that the job has been running without interruption\. | Application | You can use this metric to determine if a job is running successfully\. This metric returns \-1 for completed jobs\. | 

## Kinesis Data Streams Connector Metrics<a name="metrics-dimensions-stream"></a>

AWS emits all records for Kinesis Data Streams in addition to the following:


| Metric | Unit | Description | Level | Usage Notes | 
| --- | --- | --- | --- | --- | 
| millisBehindLatest | Milliseconds | The number of milliseconds the consumer is behind the head of the stream, indicating how far behind current time the consumer is\. | Application \(for Stream\), Parallelism \(for ShardId\) | [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/kinesisanalytics/latest/java/metrics-dimensions.html)  | 
| bytesRequestedPerFetch | Bytes | The bytes requested in a single call to getRecords\. | Application \(for Stream\), Parallelism \(for ShardId\) |  | 

## Amazon MSK Connector Metrics<a name="metrics-dimensions-msk"></a>

AWS emits all records for Amazon MSK in addition to the following:


| Metric | Unit | Description | Level | Usage Notes | 
| --- | --- | --- | --- | --- | 
| currentoffsets | N/A | The consumer's current read offset, for each partition\. A particular partition's metric can be specified by topic name and partition id\. | Application \(for Topic\), Parallelism \(for PartitionId\) |  | 
| commitsFailed | N/A | The total number of offset commit failures to Kafka, if offset committing and checkpointing are enabled\.  | Application, Operator, Task, Parallelism | Committing offsets back to Kafka is only a means to expose consumer progress, so a commit failure does not affect the integrity of Flink's checkpointed partition offsets\. | 
| commitsSucceeded | N/A | The total number of successful offset commits to Kafka, if offset committing and checkpointing are enabled\.  | Application, Operator, Task, Parallelism |  | 
| committedoffsets | N/A | The last successfully committed offsets to Kafka, for each partition\. A particular partition's metric can be specified by topic name and partition id\. | Application \(for Topic\), Parallelism \(for PartitionId\) |  | 
| records\_lag\_max | Count | The maximum lag in terms of number of records for any partition in this window | Application, Operator, Task, Parallelism |  | 
| bytes\_consumed\_rate | Bytes | The average number of bytes consumed per second for a topic | Application, Operator, Task, Parallelism |  | 

## Apache Zeppelin Metrics<a name="metrics-dimensions-zeppelin"></a>

For Studio notebooks, AWS emits the following metrics at the application level: `KPUs`, `cpuUtilization`, `heapMemoryUtilization`, `oldGenerationGCTime`, `oldGenerationGCCount`, and `threadCount`\. In addition, it emits the metrics shown in the following table, also at the application level\.


****  

| Metric | Unit | Description | Prometheus name | 
| --- | --- | --- | --- | 
| zeppelinCpuUtilization | Percentage | Overall percentage of CPU utilization in the Apache Zeppelin server\. | process\_cpu\_usage | 
| zeppelinHeapMemoryUtilization | Percentage | Overall percentage of heap memory utilization for the Apache Zeppelin server\. | jvm\_memory\_used\_bytes | 
| zeppelinThreadCount | Count | The total number of live threads used by the Apache Zeppelin server\. | jvm\_threads\_live\_threads | 
| zeppelinWaitingJobs | Count | The number of queued Apache Zeppelin jobs waiting for a thread\. | jetty\_threads\_jobs | 
| zeppelinServerUptime | Seconds | The total time that the server has been up and running\. | process\_uptime\_seconds | 

## Viewing CloudWatch Metrics<a name="metrics-dimensions-viewing"></a>

You can view CloudWatch metrics for your application using the Amazon CloudWatch console or the AWS CLI\.

**To view metrics using the CloudWatch console**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. In the navigation pane, choose **Metrics**\.

1. In the **CloudWatch Metrics by Category** pane for Amazon Kinesis Data Analytics, choose a metrics category\.

1. In the upper pane, scroll to view the full list of metrics\.

**To view metrics using the AWS CLI**
+ At a command prompt, use the following command\.

  ```
  1. aws cloudwatch list-metrics --namespace "AWS/KinesisAnalytics" --region region
  ```

## Setting CloudWatch Metrics Reporting Levels<a name="cloudwatch-logs-levels"></a>

You can control the level of application metrics that your application creates\. Kinesis Data Analytics for Apache Flink supports the following metrics levels:
+ **Application:** The application only reports the highest level of metrics for each application\. Kinesis Data Analytics metrics are published at the Application level by default\.
+ **Task:** The application reports task\-specific metric dimensions for metrics defined with the Task metric reporting level, such as number of records in and out of the application per second\.
+ **Operator:** The application reports operator\-specific metric dimensions for metrics defined with the Operator metric reporting level, such as metrics for each filter or map operation\.
+ **Parallelism:** The application reports `Task` and `Operator` level metrics for each execution thread\. This reporting level is not recommended for applications with a Parallelism setting above 64 due to excessive costs\. 
**Note**  
You should only use this metric level for troubleshooting because of the amount of metric data that the service generates\. You can only set this metric level using the CLI\. This metric level is not available in the console\.

The default level is **Application**\. The application reports metrics at the current level and all higher levels\. For example, if the reporting level is set to **Operator**, the application reports **Application**, **Task**, and **Operator** metrics\.

You set the CloudWatch metrics reporting level using the `MonitoringConfiguration` parameter of the [https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_CreateApplication.html](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_CreateApplication.html) action, or the `MonitoringConfigurationUpdate` parameter of the [https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_UpdateApplication.html](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_UpdateApplication.html) action\. The following example request for the [https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_UpdateApplication.html](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_UpdateApplication.html) action sets the CloudWatch metrics reporting level to **Task**:

```
{
   "ApplicationName": "MyApplication",  
   "CurrentApplicationVersionId": 4,
   "ApplicationConfigurationUpdate": { 
      "FlinkApplicationConfigurationUpdate": { 
         "MonitoringConfigurationUpdate": { 
            "ConfigurationTypeUpdate": "CUSTOM",
            "MetricsLevelUpdate": "TASK"
         }
      }
   }
}
```

You can also configure the logging level using the `LogLevel` parameter of the [https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_CreateApplication.html](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_CreateApplication.html) action or the `LogLevelUpdate` parameter of the [https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_UpdateApplication.html](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_UpdateApplication.html) action\. You can use the following log levels:
+ `ERROR`: Logs potentially recoverable error events\.
+ `WARN`: Logs warning events that might lead to an error\.
+ `INFO`: Logs informational events\.
+ `DEBUG`: Logs general debugging events\. 

For more information about Log4j logging levels, see [Custom Log Levels](https://logging.apache.org/log4j/2.x/manual/customloglevels.html) in the [Apache Log4j](https://logging.apache.org/log4j/2.x/) documentation\.