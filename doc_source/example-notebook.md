# Tutorial: Creating a Studio notebook in Kinesis Data Analytics<a name="example-notebook"></a>

The following tutorial demonstrates how to create a Studio notebook that reads data from a Kinesis Data Stream or an Amazon MSK cluster\.

**Topics**
+ [Setup](#example-notebook-setup)
+ [Create an AWS Glue Database](#example-notebook-glue)
+ [Next Steps](#examples-notebook-nextsteps)
+ [Creating a Studio notebook with Kinesis Data Streams](example-notebook-streams.md)
+ [Creating a Studio notebook with Amazon MSK](example-notebook-msk.md)
+ [Cleaning up your application and dependent resources](example-notebook-cleanup.md)

## Setup<a name="example-notebook-setup"></a>

Ensure that your AWS CLI is version 2 or later\. To install the latest AWS CLI, see [ Installing, updating, and uninstalling the AWS CLI version 2](https://docs.aws.amazon.com/cli/latest/userguide/install-cliv2.html)\.

## Create an AWS Glue Database<a name="example-notebook-glue"></a>

Your Studio notebook uses an [AWS Glue](https://docs.aws.amazon.com/glue/latest/dg/what-is-glue.html) database for metadata about your Amazon MSK data source\.

**Create an AWS Glue Database**

1. Open the AWS Glue console at [https://console\.aws\.amazon\.com/glue/](https://console.aws.amazon.com/glue/)\.

1. Choose **Add database**\. In the **Add database** window, enter **default** for **Database name**\. Choose **Create**\. 

## Next Steps<a name="examples-notebook-nextsteps"></a>

With this tutorial, you can create a Studio notebook that uses either Kinesis Data Streams or Amazon MSK:
+ [Kinesis Data Streams](example-notebook-streams.md) : With Kinesis Data Streams, you quickly create an application that uses a Kinesis data stream as a source\. You only need to create a Kinesis data stream as a dependent resource\.
+ [Amazon MSK](example-notebook-msk.md) : With Amazon MSK, you create an application that uses a Amazon MSK cluster as a source\. You need to create an Amazon VPC, an Amazon EC2 client instance, and an Amazon MSK cluster as dependent resources\.