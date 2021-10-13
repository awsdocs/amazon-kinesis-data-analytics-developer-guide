# Kinesis Data Analytics for Apache Flink and Studio Notebook Quota<a name="limits"></a>

When working with Amazon Kinesis Data Analytics for Apache Flink, note the following quota:
+ You can create up to 50 Kinesis Data Analytics applications per Region in your account\. You can create a case to request additional applications via the service quota increase form\. For more information, see the [AWS Support Center](https://console.aws.amazon.com/support/home#/)\.

  For a list of Regions that support Kinesis Data Analytics, see [Kinesis Data Analytics Regions and Endpoints](https://docs.aws.amazon.com/general/latest/gr/rande.html#ka_region)\.

   
+ The number of Kinesis processing units \(KPU\) is limited to 32 by default\. For instructions on how to request an increase to this quota, see **To request a quota increase** in [Service Quotas](https://docs.aws.amazon.com/general/latest/gr/aws_service_limits.html)\.

   

  With Kinesis Data Analytics, your AWS account is charged for allocated resources, rather than resources that your application uses\. You are charged an hourly rate based on the maximum number of KPUs that are used to run your stream\-processing application\. A single KPU provides you with 1 vCPU and 4 GiB of memory\. For each KPU, the service also provisions 50 GiB of running application storage\.

   
+ You can create up to 1,000 Kinesis Data Analytics [Snapshots](how-fault-snapshot.md) per application\. 

   
+ You can assign up to 50 tags per application\.

   
+ The maximum size for an application JAR file is 512 MiB\. If you exceed this quota, your application will fail to start\.

   

For Studio notebooks, the following quotas apply\. To request higher quotas, [create a support case](https://console.aws.amazon.com/support/home#/)\.
+ `websocketMessageSize` = 5 MiB
+ `noteSize` = 5 MiB
+ `noteCount` = 1000