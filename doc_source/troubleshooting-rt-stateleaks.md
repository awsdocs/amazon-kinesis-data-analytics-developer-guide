# Unbounded State Growth<a name="troubleshooting-rt-stateleaks"></a>

If your application is not properly disposing of outdated state information, it will continually accumulate and lead to application performance or stability issues\. This section describes symptoms and troubleshooting steps for this condition\.

## Symptoms<a name="troubleshooting-rt-stateleaks-symptoms"></a>

This condition can have the following symptoms:
+ The `lastCheckpointDuration` metric is gradually increasing or spiking\.
+ The `lastCheckpointSize` metric is gradually increasing or spiking\.

## Causes and Solutions<a name="troubleshooting-rt-stateleaks-causes"></a>

The following conditions may cause your application to accumulate state data: 
+ Your application is retaining state data longer than it is needed\.
+ Your application uses window queries with too long a duration\.
+ You did not set TTL for your state data\. For more information, see [ State Time\-To\-Live \(TTL\)](https://ci.apache.org/projects/flink/flink-docs-release-1.11/dev/stream/state/state.html#state-time-to-live-ttl) in the [Apache Flink Documentation](https://ci.apache.org/projects/flink/flink-docs-release-1.11/)\.
+ You are running an application that depends on Apache Beam version 2\.25\.0 or newer\. You can opt out of the new version of the read transform by [extending your BeamApplicationProperties](https://docs.aws.amazon.com/kinesisanalytics/latest/java/examples-beam.html#examples-beam-configure) with the key experiments and value `use_deprecated_read`\. For more information, see the [Apache Beam Documentation](https://beam.apache.org/blog/beam-2.25.0/#highlights)\.

Sometimes applications are facing ever growing state size growth, which is not sustainable in the long term \(a Flink application runs indefinitely, after all\)\. Sometimes, this can be traced back to applications storing data in state and not aging out old information properly\. But sometimes there are just unreasonable expectations on what Flink can deliver\. Applications can use aggregations over large time windows spanning days or even weeks\. Unless [AggregateFunctions](https://nightlies.apache.org/flink/flink-docs-stable/docs/dev/datastream/operators/windows/#aggregatefunction) are used, which allow incremental aggregations, Flink needs to keep the events of the entire window in state\.

Moreover, when using process functions to implement custom operators, the application needs to remove data from state that is no longer required for the business logic\. In that case, [state time\-to\-live](https://nightlies.apache.org/flink/flink-docs-stable/docs/dev/datastream/fault-tolerance/state/#state-time-to-live-ttl) can be used to automatically age out data based on processing time\. Kinesis Data Analytics is using incremental checkpoints and thus state ttl is based on [RocksDB compaction](https://github.com/facebook/rocksdb/wiki/Compaction)\. You can only observe an actual reduction in state size \(indicated by checkpoint size\) after a compaction operation occurs\. In particular for checkpoint sizes below 200 MB, it's unlikely that you observe any checkpoint size reduction as a result of state expiring\. However, savepoints are based on a clean copy of the state that does not contain old data, so you can trigger a snapshot in Kinesis Data Analytics to force the removal of outdated state\.

For debugging purposes, it can make sense to disable incremental checkpoints to verify more quickly that the checkpoint size actually decreases or stabilizes \(and avoid the effect of compaction in RocksBS\)\. This requires a ticket to the service team, though\. 