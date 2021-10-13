# Resilience in Amazon Kinesis Data Analytics for Apache Flink<a name="disaster-recovery-resiliency"></a>

The AWS global infrastructure is built around AWS Regions and Availability Zones\. AWS Regions provide multiple physically separated and isolated Availability Zones, which are connected with low\-latency, high\-throughput, and highly redundant networking\. With Availability Zones, you can design and operate applications and databases that automatically fail over between Availability Zones without interruption\. Availability Zones are more highly available, fault tolerant, and scalable than traditional single or multiple data center infrastructures\. 

For more information about AWS Regions and Availability Zones, see [AWS Global Infrastructure](https://aws.amazon.com/about-aws/global-infrastructure/)\.

In addition to the AWS global infrastructure, Kinesis Data Analytics for Apache Flink offers several features to help support your data resiliency and backup needs\.

## Disaster Recovery<a name="disaster-recovery"></a>

Kinesis Data Analytics runs in a serverless mode, and takes care of host degradations, Availability Zone availability, and other infrastructure related issues by performing automatic migration\. Kinesis Data Analytics achieves this through multiple, redundant mechanisms\. Each Kinesis Data Analytics application using Apache Flink runs in a single\-tenant Apache Flink cluster\. The Apache Flink cluster is run with the JobMananger in high availability mode using Zookeeper across multiple availability zones\. Kinesis Data Analytics deploys Apache Flink using Amazon EKS\. Multiple Kubernetes pods are used in Amazon EKS for each AWS region across availability zones\. In the event of a failure, Kinesis Data Analytics first tries to recover the application within the running Apache Flink cluster using your application’s checkpoints, if available\.

Kinesis Data Analytics for Apache Flink backs up application state using *Checkpoints* and *Snapshots*:
+ *Checkpoints* are backups of application state that Kinesis Data Analytics automatically creates periodically and uses to restore from faults\.
+ *Snapshots* are backups of application state that you create and restore from manually\.

For more information about checkpoints and snapshots, see [Fault Tolerance](how-fault.md)\.

## Versioning<a name="versioning"></a>

Stored versions of application state are versioned as follows:
+ *Checkpoints* are versioned automatically by the service\. If the service uses a checkpoint to restart the application, the latest checkpoint will be used\. 
+ *Savepoints* are versioned using the **SnapshotName** parameter of the [CreateApplicationSnapshot](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_CreateApplicationSnapshot.html) action\.

Kinesis Data Analytics encrypts data stored in checkpoints and savepoints\.