# Application State Data is Accumulating<a name="troubleshooting-rt-stateleaks"></a>

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