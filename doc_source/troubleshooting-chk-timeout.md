# Checkpointing is timing out<a name="troubleshooting-chk-timeout"></a>

If your application is not optimized or properly provisioned, checkpoints can fail\. This section describes symptoms and troubleshooting steps for this condition\. 

## Symptoms<a name="troubleshooting-chk-timeout-symptoms"></a>

If checkpoints fail for your application, the `numberOfFailedCheckpoints` will be greater than zero\. 

Checkpoints can fail due to either direct failures, such as application errors, or due to transient failures, such as running out of application resources\. Check your application logs and metrics for the following symptoms:
+ Errors in your code\.
+ Errors accessing your application's dependent services\.
+ Errors serializing data\. If the default serializer can't serialize your application data, the application will fail\. For information about using a custom serializer in your application, see [Custom Serializers](https://ci.apache.org/projects/flink/flink-docs-release-1.11/dev/custom_serializers.html) in the [Apache Flink Documentation](https://ci.apache.org/projects/flink/flink-docs-release-1.11/)\.
+ Out of Memory errors\.
+ Spikes or steady increases in the following metrics:
  + `heapMemoryUtilization`
  + `oldGenerationGCTime`
  + `oldGenerationGCCount`
  + `lastCheckpointSize`
  + `lastCheckpointDuration`

For more information about monitoring checkpoints, see [Monitoring Checkpointing](https://ci.apache.org/projects/flink/flink-docs-release-1.11/monitoring/checkpoint_monitoring.html) in the [Apache Flink Documentation](https://ci.apache.org/projects/flink/flink-docs-release-1.11/)\.

## Causes and Solutions<a name="troubleshooting-chk-timeout-causes"></a>

Your application log error messages show the cause for direct failures\. Transient failures can have the following causes:
+ Your application has insufficient KPU provisioning\. For information about increasing application provisioning, see [Scaling](how-scaling.md)\.
+ Your application state size is too large\. You can monitor your application state size using the `lastCheckpointSize` metric\.
+ Your application's state data is unequally distributed between keys\. If your application uses the `KeyBy` operator, ensure that your incoming data is being divided equally between keys\. If most of the data is being assigned to a single key, this creates a bottleneck that causes failures\.
+ Your application is experiencing memory or garbage collection backpressure\. Monitor your application's `heapMemoryUtilization`, `oldGenerationGCTime`, and `oldGenerationGCCount` for spikes or steadily increasing values\.