# Best Practices for Kinesis Data Analytics for Apache Flink<a name="best-practices"></a>

This section contains information and recommendations for developing stable, performant Amazon Kinesis Data Analytics applications\.

**Topics**
+ [Fault tolerance: checkpoints and savepoints](#how-dev-bp-checkpoint)
+ [Performance and parallelism](#how-dev-bp-performance)
+ [Logging](#how-dev-bp-logging)
+ [Coding](#how-dev-bp-code)
+ [Studio notebook refresh interval](#notebook-refresh-rate)
+ [Studio notebook optimum performance](#notebook-refresh-rate)

## Fault tolerance: checkpoints and savepoints<a name="how-dev-bp-checkpoint"></a>

Use checkpoints and savepoints to implement fault tolerance in your Kinesis Data Analytics for Apache Flink application\. Keep the following in mind when developing and maintaining your application:
+ We recommend that you leave checkpointing enabled for your application\. Checkpointing provides fault tolerance for your application during scheduled maintenance, as well as in case of unexpected failures due to service issues, application dependency failures, and other issues\. For information about scheduled maintenance, see [Kinesis Data Analytics for Apache Flink Maintenance](maintenance.md)\.
+ Set [ApplicationSnapshotConfiguration::SnapshotsEnabled](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_ApplicationSnapshotConfiguration.html) to `false` during application development or troubleshooting\. A snapshot is created during every application stop, which may cause issues if the application is in an unhealthy state or isn't performant\. Set `SnapshotsEnabled` to `true` after the application is in production and is stable\.
**Note**  
We recommend that your application create a snapshot several times a day to restart properly with correct state data\. The correct frequency for your snapshots depends on your application's business logic\. Taking frequent snapshots allows you to recover more recent data, but increases cost and requires more system resources\.

  For information about monitoring application downtime, see [Metrics and Dimensions](metrics-dimensions.md)\.

For more information about implementing fault tolerance, see [Fault Tolerance](how-fault.md)\.

## Performance and parallelism<a name="how-dev-bp-performance"></a>

Your application can scale to meet any throughput level by tuning your application parallelism, and avoiding performance pitfalls\. Keep the following in mind when developing and maintaining your application:
+ Verify that all of your application sources and sinks are sufficiently provisioned and are not being throttled\. If the sources and sinks are other AWS services, monitor those services using [CloudWatch](https://docs.aws.amazon.com/cloudwatch/?id=docs_gateway)\.
+ For applications with very high parallelism, check if the high levels of parallelism are applied to all operators in the application\. By default, Apache Flink applies the same application parallelism for all operators in the application graph\. This can lead to either provisioning issues on sources or sinks, or bottlenecks in operator data processing\. You can change the parallelism of each operator in code with [setParallelism](https://ci.apache.org/projects/flink/flink-docs-release-1.11/dev/parallel.html)\.
+ Understand the meaning of the parallelism settings for the operators in your application\. If you change the parallelism for an operator, you may not be able to restore the application from a snapshot created when the operator had a parallelism that is incompatible with the current settings\. For more information about setting operator parallelism, see [ Set maximum parallelism for operators explicitly](https://ci.apache.org/projects/flink/flink-docs-release-1.11/ops/production_ready.html#set-maximum-parallelism-for-operators-explicitly)\.

For more information about implementing scaling, see [Scaling](how-scaling.md)\.

## Logging<a name="how-dev-bp-logging"></a>

You can monitor your application's performance and error conditions using CloudWatch Logs\. Keep the following in mind when configuring logging for your application: 
+ Enable CloudWatch logging for the application so that any runtime issues can be debugged\.
+ Do not create a log entry for every record being processed in the application\. This causes severe bottlenecks during processing and might lead to backpressure in processing of data\.
+ Create CloudWatch alarms to notify you when your application is not running properly\. For more information, see [Alarms](monitoring-metrics-alarms.md)

For more information about implementing logging, see [Logging and Monitoring](monitoring-overview.md)\.

## Coding<a name="how-dev-bp-code"></a>

You can make your application performant and stable by using recommended programming practices\. Keep the following in mind when writing application code:
+ Do not use `system.exit()` in your application code, in either your application's `main` method or in user\-defined functions\. If you want to shut down your application from within code, throw an exception derived from `Exception` or `RuntimeException`, containing a message about what went wrong with the application\. 

  Note the following about how the service handles this exception:
  + If the exception is thrown from your application's `main` method, the service will wrap it in a `ProgramInvocationException` when the application transitions to the `RUNNING` status, and the job manager will fail to submit the job\.
  + If the exception is thrown from a user\-defined function, the job manager will fail the job and restart it, and details of the exception will be written to the exception log\.
+ Consider shading your application JAR file and its included dependencies\. Shading is recommended when there are potential conflicts in package names between your application and the Apache Flink runtime\. If a conflict occurs, your application logs may contain an exception of type `java.util.concurrent.ExecutionException`\. For more information about shading your application JAR file, see [Apache Maven Shade Plugin](https://maven.apache.org/plugins/maven-shade-plugin/)\.

## Studio notebook refresh interval<a name="notebook-refresh-rate"></a>

If you change the paragraph result refresh interval, set it to a value that is at least 1000 milliseconds\.

## Studio notebook optimum performance<a name="notebook-refresh-rate"></a>

We tested with the following statement and got the best performance when `events-per-second` multiplied by `number-of-keys` was under 25,000,000\. This was for `events-per-second` under 150,000\.

```
SELECT key, sum(value) FROM key-values GROUP BY key
```