# Tracking Events in Kinesis Data Analytics for Apache Flink Using the DataStream API<a name="how-time"></a>

Kinesis Data Analytics tracks events using the following timestamps:
+ **Processing Time:** Refers to the system time of the machine that is executing the respective operation\.
+ **Event Time:** Refers to the time that each individual event occurred on its producing device\.
+ **Ingestion Time:** Refers to the time that events enter the Kinesis Data Analytics service\.

You set the time used by the streaming environment using [https://ci.apache.org/projects/flink/flink-docs-release-1.11/api/java/org/apache/flink/streaming/api/environment/StreamExecutionEnvironment.html#setStreamTimeCharacteristic-org.apache.flink.streaming.api.TimeCharacteristic-](https://ci.apache.org/projects/flink/flink-docs-release-1.11/api/java/org/apache/flink/streaming/api/environment/StreamExecutionEnvironment.html#setStreamTimeCharacteristic-org.apache.flink.streaming.api.TimeCharacteristic-):

```
env.setStreamTimeCharacteristic(TimeCharacteristic.ProcessingTime);
env.setStreamTimeCharacteristic(TimeCharacteristic.IngestionTime);
env.setStreamTimeCharacteristic(TimeCharacteristic.EventTime);
```

For more information about timestamps, see [Event Time](https://ci.apache.org/projects/flink/flink-docs-release-1.11/dev/event_time.html) in the [Apache Flink Documentation](https://ci.apache.org/projects/flink/flink-docs-release-1.11/)\.