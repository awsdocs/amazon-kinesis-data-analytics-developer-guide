# Application is Restarting<a name="troubleshooting-rt-restarts"></a>

If your application is not healthy, its Apache Flink job continually fails and restarts\. This section describes symptoms and troubleshooting steps for this condition\.

## Symptoms<a name="troubleshooting-rt-restarts-symptoms"></a>

This condition can have the following symptoms:
+ The `FullRestarts` metric is not zero\. This metric represents the number of times the application's job has restarted since you started the application\.
+ The `Downtime` metric is not zero\. This metric represents the number of milliseconds that the application is in the `FAILING` or `RESTARTING` status\.
+ The application log contains status changes to `RESTARTING` or `FAILED`\. You can query your application log for these status changes using the following CloudWatch Logs Insights query: [Analyze Errors: Application Task\-Related Failures](cloudwatch-logs-reading.md#cloudwatch-logs-reading-apps)\.

## Causes and Solutions<a name="troubleshooting-rt-restarts-causes"></a>

The following conditions may cause your application to become unstable and repeatedly restart:
+ **Operator is Throwing an Exception:** If any exception in an operator in your application is unhandled, the application fails over \(by interpreting that the failure cannot be handled by operator\)\. The application restarts from the latest checkpoint to maintain "exactly\-once" processing semantics\. As a result, `Downtime` is not zero during these restart periods\. In order to prevent this from happening, we recommend that you handle any retryable exceptions in the application code\.

  You can investigate the causes of this condition by querying your application logs for changes from your application's state from `RUNNING` to `FAILED`\. For more information, see [Analyze Errors: Application Task\-Related Failures](cloudwatch-logs-reading.md#cloudwatch-logs-reading-apps)\.
+ **Kinesis Data Streams are not properly provisioned:** If a source or sink for your application is a Kinesis data stream, check the [metrics](https://docs.aws.amazon.com/streams/latest/dev/monitoring-with-cloudwatch.html) for the stream for `ReadProvisionedThroughputExceeded` or `WriteProvisionedThroughputExceeded` errors\.

  If you see these errors, you can increase the available throughput for the Kinesis stream by increasing the stream's number of shards\. For more information, see [ How do I change the number of open shards in Kinesis Data Streams?](https://aws.amazon.com/premiumsupport/knowledge-center/kinesis-data-streams-open-shards/)\.
+ **Other sources or sinks are not properly provisioned or available:** Verify that your application is correctly provisioning sources and sinks\. Check that any sources or sinks used in the application \(such as other AWS services, or external sources or destinations\) are well provisioned, are not experiencing read or write throttling, or are periodically unavailable\.

  If you are experiencing throughput\-related issues with your dependent services, either increase resources available to those services, or investigate the cause of any errors or unavailability\.
+ **Operators are not properly provisioned:** If the workload on the threads for one of the operators in your application is not correctly distributed, the operator can become overloaded and the application can crash\. For information about tuning operator parallelism, see [Manage operator scaling properly](performance-improving.md#performance-improving-scaling-op)\.
+ **Application fails with DaemonException: ** This error appears in your application log if you are using a version of Apache Flink prior to 1\.11\. You may need to upgrade to a later version of Apache Flink so that a KPL version of 0\.14 or later is used\. 
+ **Application fails with TimeoutException, FlinkException, or RemoteTransportException:** These errors may appear in your application log if your task managers are crashing\. If your application is overloaded, your task managers can experience CPU or memory resource pressure, causing them to fail\.

  These errors may look like the following:
  + `java.util.concurrent.TimeoutException: The heartbeat of JobManager with id xxx timed out`
  + `org.apache.flink.util.FlinkException: The assigned slot xxx was removed`
  + `org.apache.flink.runtime.io.network.netty.exception.RemoteTransportException: Connection unexpectedly closed by remote task manager`

  To troubleshoot this condition, check the following:
  + Check your CloudWatch metrics for unusual spikes in CPU or memory usage\.
  + Check your application for throughput issues\. For more information, see [Troubleshooting Performance](performance-troubleshooting.md)\.
  + Examine your application log for unhandled exceptions that your application code is raising\.
+ **Application fails with JaxbAnnotationModule Not Found error:** This error occurs if your application uses Apache Beam, but doesn't have the correct dependencies or dependency versions\. Kinesis Data Analytics applications that use Apache Beam must use the following versions of dependencies:

  ```
  <jackson.version>2.10.2</jackson.version>
  ...
  <dependency>
      <groupId>com.fasterxml.jackson.module</groupId>
      <artifactId>jackson-module-jaxb-annotations</artifactId>
      <version>2.10.2</version>
  </dependency>
  ```

  If you do not provide the correct version of `jackson-module-jaxb-annotations` as an explicit dependency, your application loads it from the environment dependencies, and since the versions do not match, the application crashes at runtime\. 

  For more information about using Apache Beam with Kinesis Data Analytics, see [Apache Beam](examples-beam.md)\.