# Throughput is Too Slow<a name="troubleshooting-rt-throughput"></a>

If your application is not processing incoming streaming data quickly enough, it will perform poorly and become unstable\. This section describes symptoms and troubleshooting steps for this condition\. 

## Symptoms<a name="troubleshooting-rt-throughput-symptoms"></a>

This condition can have the following symptoms:
+ If the data source for your application is a Kinesis stream, the stream's `MillisBehindLatest` metric continually increases\.
+ If the data source for your application is an Amazon MSK cluster, the cluster's consumer lag metrics continually increase\. For more information, see [ Consumer\-Lag Monitoring](https://docs.aws.amazon.com/msk/latest/developerguide/consumer-lag.html) in the [ Amazon MSK Developer Guide](https://docs.aws.amazon.com/msk/latest/developerguide/what-is-msk.html)\.
+ If the data source for your application is a different service or source, check any available consumer lag metrics or data available\.

## Causes and Solutions<a name="troubleshooting-rt-throughput-causes"></a>

There can be many causes for slow application throughput\. If your application is not keeping up with input, check the following:
+ If throughput lag is spiking and then tapering off, check if the application is restarting\. Your application will stop processing input while it restarts, causing lag to spike\. For information about application failures, see [Application is Restarting](troubleshooting-rt-restarts.md)\.
+ If throughput lag is consistent, check to see if your application is optimized for performance\. For information on optimizing your application's performance, see [Troubleshooting Performance](performance-troubleshooting.md)\.
+ If throughput lag is not spiking but continuously increasing, and your application is optimized for performance, you must increase your application resources\. For information on increasing application resources, see [Scaling](how-scaling.md)\.

For troubleshooting steps for slow throughput or consumer lag increasing in the application source, see [Troubleshooting Performance](performance-troubleshooting.md)\.