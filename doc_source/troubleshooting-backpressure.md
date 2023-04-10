# Backpressure<a name="troubleshooting-backpressure"></a>

Flink uses backpressure to adapt the processing speed of individual operators\. 

The operator can struggle to keep up processing the message volume it receives for many reasons\. The operation may require more CPU resources than the operator has available, The operator may wait for I/O operations to complete\. If an operator cannot process events fast enough, it build backpressure in the upstream operators feeding into the slow operator\. This causes the upstream operators to slow down, which can further propagate the backpressure to the source and cause the source to adapt to the overall throughput of the application by slowing down as well\. You can find a deeper description of backpressure and how it works at [How Apache Flink™ handles backpressure](https://www.ververica.com/blog/how-flink-handles-backpressure)\.

Knowing which operators in an applications are slow gives you crucial information to understand the root cause of performance problems in the application\. Backpressure information is [exposed through the Flink Dashboard](https://nightlies.apache.org/flink/flink-docs-stable/docs/ops/monitoring/back_pressure/)\. To identify the slow operator, look for the operator with a high backpressure value that is closest to a sink \(operator B in the following example\)\. The operator causing the slowness is then one of the downstream operators \(operator C in the example\)\. B could process events faster, but is backpressured as it cannot forward the output to the actual slow operator C\.

```
A (backpressured 93%) -> B (backpressured 85%) -> C (backpressured 11%) -> D (backpressured 0%)
```

Once you have identified the slow operator, try to understand why it's slow\. There could be a myriad of reasons and sometimes it's not obvious what's wrong and can require days of debugging and profiling to resolve\. Following are some obvious and more common reasons, some of which are further explained below:
+ The operator is doing slow I/O, e\.g\., network calls \(consider using AsyncIO instead\)\.
+ There is a skew in the data and one operator is receiving more events than others \(verify by looking at the number of messages in/out of individual subtasks \(i\.e\., instances of the same operator\) in the Flink dashboard\.
+ It's a resource intensive operation \(if there is no data skew consider scaling out for CPU/memory bound work or increasing `ParallelismPerKPU` for I/O bound work\)
+ Extensive logging in the operator \(reduce the logging to a minimum for production application or consider sending debug output to a data stream instead\)\.

## Testing Throughput with the Discarding Sink<a name="troubleshooting-testing-throughput"></a>

The [Discarding Sink](https://nightlies.apache.org/flink/flink-docs-stable/api/java/org/apache/flink/streaming/api/functions/sink/DiscardingSink.html) simply disregards all events it receives while still executing the application \(an application without any sink fails to execute\)\. This is very useful for throughput testing, profiling, and to verify if the application is scaling properly\. It's also a very pragmatic sanity check to verify if the sinks are causing back pressure or the application \(but just checking the backpressure metrics is often easier and more straightforward\)\.

By replacing all sinks of an application with a discarding sink and creating a mock source that generates data that r esembles production data, you can measure the maximum throughput of the application for a certain parallelism setting\. You can then also increase the parallelism to verify that the application scales properly and does not have a bottleneck that only emerges at higher throughput \(e\.g\., because of data skew\)\.