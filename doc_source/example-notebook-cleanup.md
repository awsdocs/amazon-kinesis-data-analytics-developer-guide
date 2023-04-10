# Cleaning up your application and dependent resources<a name="example-notebook-cleanup"></a>

## Delete your Studio notebook<a name="example-notebook-cleanup-app"></a>

1. Open the Kinesis Data Analytics console\.

1. Choose **MyNotebook**\.

1. Choose **Actions**, then **Delete**\.

## Delete your AWS Glue Database and Connection<a name="example-notebook-cleanup-glue"></a>

1. Open the AWS Glue console at [https://console\.aws\.amazon\.com/glue/](https://console.aws.amazon.com/glue/)\.

1. Choose **Databases** from the left navigation bar\. Check the checkbox next to **Default** to select it\. Choose **Action**, **Delete Database**\. Confirm your selection\.

1. Choose **Connections** from the left navigation bar\. Check the checkbox next to **ZeppelinConnection** to select it\. Choose **Action**, **Delete Connection**\. Confirm your selection\.

## Delete your IAM role and policy<a name="example-notebook-msk-cleanup-iam"></a>

1. Open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. Choose **Roles** from the left navigation bar\.

1. Use the search bar to search for the **ZeppelinRole** role\.

1. Choose the **ZeppelinRole** role\. Choose **Delete Role**\. Confirm the deletion\.

## Delete your CloudWatch log group<a name="example-notebook-cleanup-cw"></a>

The console creates a CloudWatch Logs group and log stream for you when you create your application using the console\. You do not have a log group and stream if you created your application using the AWS CLI\.

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. Choose **Log groups** from the left navigation bar\.

1. Choose the **/aws/kinesis\-analytics/MyNotebook** log group\.

1. Choose **Actions**, **Delete log group\(s\)**\. Confirm the deletion\.

## Clean up Kinesis Data Streams Resources<a name="example-notebook-cleanup-streams"></a>

To delete your Kinesis stream, open the Kinesis Data Streams console, select your Kinesis stream, and choose **Actions**, **Delete**\.

## Clean up MSK resources<a name="example-notebook-cleanup-msk"></a>

Follow the steps in this section if you created an Amazon MSK cluster for this tutorial\. This section has directions for cleaning up your Amazon EC2 client instance, Amazon VPC, and Amazon MSK cluster\.

### Delete your Amazon MSK Cluster<a name="example-notebook-msk-cleanup-msk"></a>

Follow these steps if you created an Amazon MSK cluster for this tutorial\.

1. Open the Amazon MSK console at [https://console\.aws\.amazon\.com/msk/home?region=us\-east\-1\#/home/](https://console.aws.amazon.com/msk/home?region=us-east-1#/home/)\.

1. Choose **AWSKafkaTutorialCluster**\. Choose **Delete**\. Enter **delete** in the window that appears, and confirm your selection\.

### Terminate your client instance<a name="example-notebook-msk-cleanup-client"></a>

Follow these steps if you created an Amazon EC2 client instance for this tutorial\.

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. Choose **Instances** from the left navigation bar\.

1. Choose the checkbox next to **ZeppelinClient** to select it\.

1. Choose **Instance State**, **Terminate Instance**\.

### Delete your Amazon VPC<a name="example-notebook-msk-cleanup-vpc"></a>

Follow these steps if you created an Amazon VPC for this tutorial\.

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. Choose **Network Interfaces** from the left navigation bar\.

1. Enter your VPC ID in the search bar and press enter to search\.

1. Select the checkbox in the table header to select all the displayed network interfaces\.

1. Choose **Actions**, **Detach**\. In the window that appears, choose **Enable** under **Force detachment**\. Choose **Detach**, and wait for all of the network interfaces to reach the **Available** status\.

1. Select the checkbox in the table header to select all the displayed network interfaces again\.

1. Choose **Actions**, **Delete**\. Confirm the action\.

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. Select **AWSKafkaTutorialVPC**\. Choose **Actions**, **Delete VPC**\. Enter **delete** and confirm the deletion\.