# Monitoring Performance<a name="performance-monitoring"></a>

This section describes tools for monitoring an application's performance\.

## Performance Monitoring using CloudWatch Metrics<a name="performance-monitoring-metrics"></a>

You monitor your application's resource usage, throughput, checkpointing, and downtime using CloudWatch metrics\. For information about using CloudWatch metrics with your Kinesis Data Analytics application, see [Metrics and Dimensions](metrics-dimensions.md)\.

## Performance Monitoring using CloudWatch Logs and Alarms<a name="performance-monitoring-logs"></a>

You monitor error conditions that could potentially cause performance issues using CloudWatch Logs\. 

Error conditions appear in log entries as Apache Flink job status changes from the `RUNNING` status to the `FAILED` status\. 

You use CloudWatch alarms to create notifications for performance issues, such as resource use or checkpoint metrics above a safe threshold, or unexpected application status changes\.

For information about creating CloudWatch alarms for a Kinesis Data Analytics application, see [Alarms](monitoring-metrics-alarms.md)\.