# Apache Flink Settings<a name="reference-flink-settings.title"></a>

Kinesis Data Analytics for Apache Flink is an implementation of the Apache Flink framework\. Kinesis Data Analytics uses the default values described in this section\. Some of these values can be set by Kinesis Data Analytics applications in code, and others cannot be changed\.

**Topics**
+ [State Backend](#reference-defaults-state-backend)
+ [Checkpointing](#reference-defaults-checkpoint)
+ [Savepointing](#reference-defaults-savepoint)
+ [Heap Sizes](#reference-defaults-heap)

## State Backend<a name="reference-defaults-state-backend"></a>

Kinesis Data Analytics stores transient data in a state backend\. Kinesis Data Analytics uses the **RocksDBStateBackend**\. Calling `setStateBackend` to set a different backend has no effect\. 

We enable the following features on the state backend:
+ Incremental state backend snapshots
+ Asynchronous state backend snapshots
+ Local recovery of checkpoints

In Kinesis Data Analytics, the `state.backend.rocksdb.ttl.compaction.filter.enabled` configuration is enabled by default\. Using this filter, you can update your application code to enable the compaction cleanup strategy\. For more information, see [State TTL in Flink 1\.8\.0](https://flink.apache.org/2019/05/19/state-ttl.html) in the [Apache Flink documentation](https://ci.apache.org/projects/flink/flink-docs-release-1.11/)\.

For more information about state backends, see [State Backends](https://ci.apache.org/projects/flink/flink-docs-release-1.11/ops/state/state_backends.html) in the [Apache Flink documentation](https://ci.apache.org/projects/flink/flink-docs-release-1.11/)\.

## Checkpointing<a name="reference-defaults-checkpoint"></a>

Kinesis Data Analytics for Apache Flink uses a default checkpoint configuration with the following values\. Some of these vales can be changed\. You must set [CheckpointConfiguration\.ConfigurationType](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_CheckpointConfiguration.html) to `CUSTOM` for Kinesis Data Analytics to use modified checkpointing values\.


****  

| Setting | Can be modified? | Default Value | 
| --- | --- | --- | 
| CheckpointingEnabled | Modifiable | True | 
| CheckpointInterval | Modifiable | 60000 | 
| MinPauseBetweenCheckpoints | Modifiable | 5000 | 
| Number of Concurrent Checkpoints | Not Modifiable | 1 | 
| Checkpointing Mode | Not Modifiable | Exactly Once | 
| Checkpoint Retention Policy | Not Modifiable | On Failure | 
| Checkpoint Timeout | Not Modifiable | 60 minutes | 
| Max Checkpoints Retained | Not Modifiable | 1 | 
| Restart Strategy | Not Modifiable | Fixed Delay, with infinite retries every 10 seconds\. | 
| Checkpoint and Savepoint Location | Not Modifiable | We store durable checkpoint and savepoint data to a service\-owned S3 bucket\. | 
| State Backend Memory Threshold | Not Modifiable | 1048576 | 

## Savepointing<a name="reference-defaults-savepoint"></a>

By default, when restoring from a savepoint, the resume operation will try to map all state of the savepoint back to the program you are restoring with\. If you dropped an operator, by default, restoring from a savepoint that has data that corresponds to the missing operator will fail\. You can allow the operation to succeed by setting the *AllowNonRestoredState* parameter of the application's [FlinkRunConfiguration](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_FlinkRunConfiguration.html) to `true`\. This will allow the resume operation to skip state that cannot be mapped to the new program\.

For more information, see [ Allowing Non\-Restored State](https://ci.apache.org/projects/flink/flink-docs-release-1.11/ops/state/savepoints.html#allowing-non-restored-state) in the [Apache Flink documentation](https://ci.apache.org/projects/flink/flink-docs-release-1.11/)\.

## Heap Sizes<a name="reference-defaults-heap"></a>

Kinesis Data Analytics allocates each KPU 3 GiB of JVM heap, and reserves 1 GiB for native code allocations\. For information about increasing your application capacity, see [Application Scaling in Kinesis Data Analytics for Apache Flink](how-scaling.md)\. 

For more information about JVM heap sizes, see [Configuration](https://ci.apache.org/projects/flink/flink-docs-release-1.11/ops/config.html) in the [Apache Flink documentation](https://ci.apache.org/projects/flink/flink-docs-release-1.11/)\.