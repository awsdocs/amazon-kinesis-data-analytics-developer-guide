# Resilience in Amazon Kinesis Data Analytics<a name="disaster-recovery-resiliency"></a>

**Warning**  
For new projects, we recommend that you use the new Kinesis Data Analytics Studio over Kinesis Data Analytics for SQL Applications\. Kinesis Data Analytics Studio combines ease of use with advanced analytical capabilities, enabling you to build sophisticated stream processing applications in minutes\.

The AWS global infrastructure is built around AWS Regions and Availability Zones\. AWS Regions provide multiple physically separated and isolated Availability Zones, which are connected with low\-latency, high\-throughput, and highly redundant networking\. With Availability Zones, you can design and operate applications and databases that automatically fail over between Availability Zones without interruption\. Availability Zones are more highly available, fault tolerant, and scalable than traditional single or multiple data center infrastructures\. 

For more information about AWS Regions and Availability Zones, see [AWS Global Infrastructure](https://aws.amazon.com/about-aws/global-infrastructure/)\.

In addition to the AWS global infrastructure, Kinesis Data Analytics offers several features to help support your data resiliency and backup needs\.

## Disaster Recovery<a name="disaster-recovery"></a>

Kinesis Data Analytics runs in a serverless mode, and takes care of host degradations, Availability Zone availability, and other infrastructure related issues by performing automatic migration\. When this happens, Kinesis Data Analytics ensures that the application is processed without any loss of data\. For more information, see [Delivery Model for Persisting Application Output to an External Destination](failover-checkpoint.md)\.