# Automatically Scaling Applications to Increase Throughput<a name="how-it-works-autoscaling"></a>

Amazon Kinesis Data Analytics elastically scales your application to accommodate the data throughput of your source stream and your query complexity for most scenarios\. Kinesis Data Analytics provisions capacity in the form of Kinesis Processing Units \(KPU\)\. A single KPU provides you with the memory \(4 GB\) and corresponding computing and networking\.

The default limit for KPUs for your application is eight\. For instructions on how to request an increase to this limit, see **To request a limit increase** in [AWS Service Limits](https://docs.aws.amazon.com/general/latest/gr/aws_service_limits.html)\.

**Note**  
The drop\-down item that is used to select a limit increase for KPUs is not yet available\. When requesting an increase, choose the following options on the support form:  
**Regarding:** Service limit increase
**Limit Type:** Kinesis Analytics
**Region:** *Select your application's Region*
**Limit:** Number of applications limit
**New limit value:** 100
**Use Case Description:** Provide your application prefix, and specify that you are requesting a limit increase for KPUs\.