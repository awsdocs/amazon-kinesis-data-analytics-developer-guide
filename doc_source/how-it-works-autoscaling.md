# Automatically Scaling Applications to Increase Throughput<a name="how-it-works-autoscaling"></a>

**Warning**  
For new projects, we recommend that you use the new Kinesis Data Analytics Studio over Kinesis Data Analytics for SQL Applications\. Kinesis Data Analytics Studio combines ease of use with advanced analytical capabilities, enabling you to build sophisticated stream processing applications in minutes\.

Amazon Kinesis Data Analytics elastically scales your application to accommodate the data throughput of your source stream and your query complexity for most scenarios\. Kinesis Data Analytics provisions capacity in the form of Kinesis Processing Units \(KPU\)\. A single KPU provides you with the memory \(4 GB\) and corresponding computing and networking\.

The default limit for KPUs for your application is eight\. For instructions on how to request an increase to this limit, see **To request a limit increase** in [Amazon Service Limits](https://docs.aws.amazon.com/general/latest/gr/aws_service_limits.html)\.