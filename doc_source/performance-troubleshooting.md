# Troubleshooting Performance<a name="performance-troubleshooting"></a>

This section contains a list of symptoms that you can check to diagnose and fix performance issues\.

If your data source is a Kinesis stream, performance issues typically present as a high or increasing `MillisBehindLatest` metric\. For other sources, you can check a similar metric that represents lag in reading from the source\.

## The Data Path<a name="performance-troubleshooting-data"></a>

When investigating a performance issue with your application, consider the entire path that your data takes\. The following application components may become performance bottlenecks and create backpressure if they are not properly designed or provisioned:
+ **Data sources and destinations:** Ensure that the external resources your application interacts with are property provisioned for the throughput your application will experience\.
+ **State data:** Ensure that your application doesn't interact with the state store too frequently\. 

  You can optimize the serializer your application is using\. The default Kryo serializer can handle any serializable type, but you can use a more performant serializer if your application only stores data in POJO types\. For information about Apache Flink serializers, see [ Data Types & Serialization](                 https://ci.apache.org/projects/flink/flink-docs-release-1.8/dev/types_serialization.html) in the [Apache Flink documentation](https://ci.apache.org/projects/flink/flink-docs-release-1.8/)\.
+ **Operators:** Ensure that the business logic implemented by your operators isn't too complicated, or that you aren't creating or using resources with every record processed\. Also ensure that your application isn't creating sliding or tumbling windows too frequently\.

## Performance Troubleshooting Solutions<a name="performance-troubleshooting-solutions"></a>

This section contains potential solutions to performance issues\.

**Topics**
+ [CloudWatch Monitoring Levels](#performance-troubleshooting-solutions-monitoring)
+ [Application CPU Metric](#performance-troubleshooting-solutions-cpu)
+ [Application Parallelism](#performance-troubleshooting-solutions-parallelism)
+ [Application Logging](#performance-troubleshooting-solutions-logging)
+ [Operator Parallelism](#performance-troubleshooting-solutions-operators)
+ [Application Logic](#performance-troubleshooting-solutions-logic)
+ [Application Memory](#performance-troubleshooting-solutions-memory)

### CloudWatch Monitoring Levels<a name="performance-troubleshooting-solutions-monitoring"></a>

Verify that the CloudWatch Monitoring Levels are not set to too verbose a setting\.

The `Debug` Monitoring Log Level setting generates a large amount of traffic, which can create backpressure\. You should only use it while actively investigating issues with the application\. 

If your application has a high `Parallelism` setting, using the ` Parallelism` Monitoring Metrics Level will similarly generate a large amount of traffic that can lead to backpressure\. Only use this metrics level when `Parallelism` for your application is low, or while investigating issues with the application\.

For more information, see [Application Monitoring Levels](cloudwatch-logs.md#cloudwatch_levels)\.

### Application CPU Metric<a name="performance-troubleshooting-solutions-cpu"></a>

Check the application's `CPU` metric\. If this metric is above 75 percent, you can allow the application to allocate more resources for itself by enabling auto scaling\.

If auto scaling is enabled, the application allocates more resources if CPU usage is over 75 percent for 15 minutes\. For more information about scaling, see the [Manage scaling properly](performance-improving.md#performance-improving-scaling) section following, and the [ScalingProduction readiness](how-scaling.md)\.

**Note**  
An application will only scale automatically in response to CPU usage\. The application will not auto scale in response to other system metrics, such as `heapMemoryUtilization`\. If your application has a high level of usage for other metrics, increase your application's parallelism manually\.

### Application Parallelism<a name="performance-troubleshooting-solutions-parallelism"></a>

Increase the application's parallelism\. You update the application's parallelism using the `ParallelismConfigurationUpdate` parameter of the [UpdateApplication](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_UpdateApplication.html) action\.

 The maximum KPUs for an application is 32 by default, and can be increased by requesting a limit increase\.

It is important to also assign parallelism to each operator based on its workload, rather than just increasing application parallelism alone\. See [Operator Parallelism](#performance-troubleshooting-solutions-operators) following\.

### Application Logging<a name="performance-troubleshooting-solutions-logging"></a>

Check if the application is logging an entry for every record being processed\. Writing a log entry for each record during times when the application has high throughput will cause severe bottlenecks in data processing\. To check for this condition, query your logs for log entries that your application writes with every record it processes\. For more information about reading application logs, see [Analyzing Logs with CloudWatch Logs Insights](cloudwatch-logs-reading.md)\.

### Operator Parallelism<a name="performance-troubleshooting-solutions-operators"></a>

Verify that your application's workload is distributed evenly among worker processes\.

For information about tuning the workload of your application's operators, see [Operator scaling](performance-improving.md#performance-improving-scaling-op)\.

### Application Logic<a name="performance-troubleshooting-solutions-logic"></a>

Examine your application logic for inefficient or non\-performant operations, such as accessing an external dependency \(such as a database or a web service\), accessing application state, etc\. An external dependency can also hinder performance if it is not performant or not reliably accessible, which may lead to the external dependency returing `HTTP 500` errors\. 

If your application uses an external dependency to enrich or otherwise process incoming data, consider using asynchronous IO instead\. For more information, see [Async I/O](https://ci.apache.org/projects/flink/flink-docs-stable/dev/stream/operators/asyncio.html) in the [Apache Flink documentation](https://ci.apache.org/projects/flink/flink-docs-release-1.8/)\.

### Application Memory<a name="performance-troubleshooting-solutions-memory"></a>

Check your application for resource leaks\. If your application is not properly disposing of threads or memory, you might see the `MillisBehindLatest`, `CheckpointSize`, and `CheckpointDuration`metric spiking or gradually increasing\. This condition may also lead to task manager or job manager failures\.