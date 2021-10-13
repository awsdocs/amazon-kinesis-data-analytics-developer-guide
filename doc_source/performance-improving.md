# Performance Best Practices<a name="performance-improving"></a>

This section describes special considerations for designing an application for performance\.

## Manage scaling properly<a name="performance-improving-scaling"></a>

This section contains information about managing application\-level and operator\-level scaling\. 

**Topics**
+ [Manage application scaling properly](#performance-improving-scaling-app)
+ [Manage operator scaling properly](#performance-improving-scaling-op)

### Manage application scaling properly<a name="performance-improving-scaling-app"></a>

You can use autoscaling to handle unexpected spikes in application activity\. Your application's KPUs will increase automatically if the following criteria are met:
+ Autoscaling is enabled for the application\.
+ CPU usage remains above 75 percent for 15 minutes\.

If autoscaling is enabled, but CPU usage does not remain at this threshold, the application will not scale up KPUs\. If you experience a spike in CPU usage that does not meet this threshold, or a spike in a different usage metric such as `heapMemoryUtilization`, increase scaling manually to allow your application to handle activity spikes\.

**Note**  
If the application has automatically added more resources through auto scaling, the application will release the new resources after a period of inactivity\. Downscaling resources will temporarily affect performance\.

For more information about scaling, see [Scaling](how-scaling.md)\.

### Manage operator scaling properly<a name="performance-improving-scaling-op"></a>

You can improve your application's performance by verifying that your application's workload is distributed evenly among worker processes, and that the operators in your application have the system resources they need to be stable and performant\.

You can set the parallelism for each operator in your application's code using the `parallelism` setting\. If you don't set the parallelism for an operator, it will use the application\-level parallelism setting\. Operators that use the application\-level parallelism setting can potentially use all of the system resources available for the application, making the application unstable\.

To best determine the parallelism for each operator, consider the operator's relative resource requirements compared to the other operators in the application\. Set operators that are more resource\-intensive to a higher operator parallelism setting than less resource\-intensive operators\.

The total operator parallelism for the application is the sum of the parallelism for all the operators in the application\. You tune the total operator parallelism for your application by determining the best ratio between it and the total task slots available for your application\. A typical stable ratio of total operator parallelism to task slots is 4:1, that is, the application has one task slot available for every four operator subtasks available\. An application with more resource intensive operators may need a ratio of 3:1 or 2:1, while an application with less resource\-intensive operators may be stable with a ratio of 10:1\.

You can set the ratio for the operator using [Runtime Properties](how-properties.md), so you can tune the operator's parallelism without compiling and uploading your application code\.

The following code example demonstrates how to set operator parallelism as a tunable ratio of the current application parallelism:

```
Map<String, Properties> applicationProperties = KinesisAnalyticsRuntime.getApplicationProperties();
operatorParallelism = 
    StreamExecutionEnvironment.getParallelism() / 
    Integer.getInteger(
        applicationProperties.get("OperatorProperties").getProperty("MyOperatorParallelismRatio")
    );
```

For information about subtasks, task slots, and other application resources, see [Application Resources](how-resources.md)\.

To control the distribution of workload across your application's worker processes, use the `Parallelism` setting and the `KeyBy` partition method\. For more information, see the following topics in the [ Apache Flink documentation](https://ci.apache.org/projects/flink/flink-docs-release-1.8/):
+ [Parallel Execution](https://ci.apache.org/projects/flink/flink-docs-stable/dev/parallel.html)
+ [DataStream Transformations](https://ci.apache.org/projects/flink/flink-docs-stable/dev/stream/operators/#datastream-transformations)

## Monitor external dependency resource usage<a name="performance-improving-destinations"></a>

If there is a performance bottleneck in a destination \(such as Kinesis Streams, Kinesis Data Firehose, DynamoDB or OpenSearch Service\), your application will experience backpressure\. Verify that your external dependencies are properly provisioned for your application throughput\.

**Note**  
Failures in other services can cause failures in your application\. If you are seeing failures in your application, check the CloudWatch logs for your destination services for failures\.

## Run your Apache Flink application locally<a name="performance-improving-local"></a>

To troubleshoot memory issues, you can run your application in a local Flink installation\. This will give you access to debugging tools such as the stack trace and heap dumps that are not available when running your application in Kinesis Data Analytics\.

For information about creating a local Flink installation, see [ Local Setup Tutorial](https://ci.apache.org/projects/flink/flink-docs-release-1.11/tutorials/local_setup.html) in the [Apache Flink documentation](https://ci.apache.org/projects/flink/flink-docs-release-1.11/)\.