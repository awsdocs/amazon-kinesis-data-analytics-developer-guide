# Runtime Troubleshooting<a name="troubleshooting-runtime"></a>

This section contains information about diagnosing and fixing runtime issues with your Kinesis Data Analytics application\.

**Topics**
+ [Troubleshooting Tools](#troubleshooting-tools)
+ [Application Issues](troubleshooting-symptoms.md)
+ [Application is Restarting](troubleshooting-rt-restarts.md)
+ [Throughput is Too Slow](troubleshooting-rt-throughput.md)
+ [Unbounded State Growth](troubleshooting-rt-stateleaks.md)
+ [I/O bound operators](troubleshooting-io-bound-operators.md)
+ [Upstream or source throttling from a Kinesis data stream](troubleshooting-source-throttling.md)
+ [Checkpoints](troubleshooting-checkpoints.md)
+ [Checkpointing is timing out](troubleshooting-chk-timeout.md)
+ [Checkpoint failure for Apache Beam application](troubleshooting-chk-failure-beam.md)
+ [Backpressure](troubleshooting-backpressure.md)
+ [Data skew](troubleshooting-data-skew.md)
+ [State skew](troubleshooting-state-skew.md)
+ [Integrating with resources in different regions](troubleshooting-resources-in-different-regions.md)

## Troubleshooting Tools<a name="troubleshooting-tools"></a>

The primary tool for detecting application issues is CloudWatch alarms\. Using CloudWatch alarms, you can set thresholds for CloudWatch metrics that indicate error or bottleneck conditions in your application\. For information about recommended CloudWatch alarms, see [Using CloudWatch Alarms with Amazon Kinesis Data Analytics for Apache Flink](monitoring-metrics-alarms.md)\.