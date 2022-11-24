# Configuring Kinesis Data Analytics for Apache Flink to access Resources in an Amazon VPC<a name="vpc"></a>

You can configure a Kinesis Data Analytics application to connect to private subnets in a virtual private cloud \(VPC\) in your account\. Use Amazon Virtual Private Cloud \(Amazon VPC\) to create a private network for resources such as databases, cache instances, or internal services\. Connect your application to the VPC to access private resources during execution\. 

**Topics**
+ [Amazon VPC Concepts](#vpc-concepts)
+ [VPC Application Permissions](vpc-permissions.md)
+ [Internet and Service Access for a VPC\-Connected Kinesis Data Analytics application](vpc-internet.md)
+ [Kinesis Data Analytics VPC API](vpc-api.md)
+ [Example: Using a VPC to Access Data in an Amazon MSK Cluster](vpc-example.md)

## Amazon VPC Concepts<a name="vpc-concepts"></a>

Amazon VPC is the networking layer for Amazon EC2\. If you're new to Amazon EC2, see [What is Amazon EC2?](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/concepts.html) in the *Amazon EC2 User Guide for Linux Instances* to get a brief overview\. 

The following are the key concepts for VPCs:
+ A *virtual private cloud* \(VPC\) is a virtual network dedicated to your AWS account\. 
+ A *subnet* is a range of IP addresses in your VPC\. 
+ A *route table* contains a set of rules, called routes, that are used to determine where network traffic is directed\. 
+ An *internet gateway* is a horizontally scaled, redundant, and highly available VPC component that allows communication between instances in your VPC and the internet\. It therefore imposes no availability risks or bandwidth constraints on your network traffic\. 
+ A *VPC endpoint* enables you to privately connect your VPC to supported AWS services and VPC endpoint services powered by PrivateLink without requiring an internet gateway, NAT device, VPN connection, or AWS Direct Connect connection\. Instances in your VPC do not require public IP addresses to communicate with resources in the service\. Traffic between your VPC and the other service does not leave the Amazon network\. 

For more information about the Amazon VPC service, see the [Amazon Virtual Private Cloud User Guide](https://docs.aws.amazon.com/vpc/latest/userguide/what-is-amazon-vpc.html)\.

Kinesis Data Analytics creates [ elastic network interfaces](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_ElasticNetworkInterfaces.html) in one of the subnets provided in your VPC configuration for the application\. The number of elastic network interfaces created in your VPC subnets may vary, depending on the parallelism and parallelism per KPU of the application\. For more information about application scaling, see [ScalingProduction readiness](how-scaling.md)\.

**Note**  
VPC configurations are not supported for SQL applications\.

**Note**  
The Kinesis Data Analytics service manages the checkpoint and snapshot state for applications that have a VPC configuration\.