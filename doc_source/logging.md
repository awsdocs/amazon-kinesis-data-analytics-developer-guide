# Logging<a name="logging"></a>

Logging is important for production application to understand errors and failures\. However, the logging subsystem needs to collect and forward log entries to CloudWatch Logs While some logging is fine and desirable, extensive logging can overload the service and cause the Flink application to fall behind\. Logging exceptions and warnings is certainly a good idea\. But you cannot generate a log message for each and every message that is processed by the Flink application\. Flink is optimized for high throughout and low latency, the logging subsystem is not\. In case it is really required to generate log output for every processed message, use an additional DataStream inside the Flink application and a proper sink to send the data to Amazon S3 or CloudWatch\. Do not use the Java logging system for this purpose\. Moreover, Kinesis Data Analytics’ `Debug Monitoring Log Level` setting generates a large amount of traffic, which can create backpressure\. You should only use it while actively investigating issues with the application\.

## Querying Logs with CloudWatch Logs Insights<a name="logging-querying"></a>

CloudWatch Logs Insights is a powerful service to query log at scale\. Customers should leverage its capabilities to quickly search through logs to identify and mitigate errors during operational events\.

 The following query looks for exceptions in all task manager logs and orders them according to the time they occurred\.

```
fields @timestamp, @message
| filter isPresent(throwableInformation.0) or isPresent(throwableInformation) or @message like /(Error|Exception)/
| sort @timestamp desc
```

For other useful queries, see [Example Queries](https://docs.aws.amazon.com/kinesisanalytics/latest/java/cloudwatch-logs-reading.html#cloudwatch-logs-reading-examples)\.