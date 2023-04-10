# Clean Up AWS Resources<a name="gs-table-cleanup"></a>

This section includes procedures for cleaning up AWS resources created in the Getting Started \(Table API\) tutorial\.

**Topics**
+ [Delete Your Kinesis Data Analytics Application](#gs-table-cleanup-app)
+ [Delete Your Amazon MSK Cluster](#gs-table-cleanup-msk)
+ [Delete Your VPC](#gs-table-cleanup-vpc)
+ [Delete Your Amazon S3 Objects and Bucket](#gs-table-cleanup-s3)
+ [Delete Your IAM Resources](#gs-table-cleanup-iam)
+ [Delete Your CloudWatch Resources](#gs-table-cleanup-cw)
+ [Next Step](#gs-table-cleanup-next-step-5)

## Delete Your Kinesis Data Analytics Application<a name="gs-table-cleanup-app"></a>

Use the following procedure to delete the application\.

**To delete the application**

1. Open the Kinesis console at [https://console\.aws\.amazon\.com/kinesis](https://console.aws.amazon.com/kinesis)\.

1. In the Kinesis Data Analytics panel, choose **MyApplication**\.

1. On the application page, choose **Delete** and then confirm the deletion\.

## Delete Your Amazon MSK Cluster<a name="gs-table-cleanup-msk"></a>

To delete your Amazon MSK cluster, follow [ Step 8: Delete the Amazon MSK Cluster](https://docs.aws.amazon.com/msk/latest/developerguide/delete-cluster.html) in the [ Amazon Managed Streaming for Apache Kafka Developer Guide](https://docs.aws.amazon.com/msk/latest/developerguide/what-is-msk.html)\.

## Delete Your VPC<a name="gs-table-cleanup-vpc"></a>

To delete your Amazon VPC, do the following:
+ Open the Amazon VPC console\.
+ Choose your VPC\.
+ For **Actions**, choose **Delete VPC**\.

## Delete Your Amazon S3 Objects and Bucket<a name="gs-table-cleanup-s3"></a>

Use the following procedure to delete your S3 objects and bucket\.

**To delete your S3 objects and bucket**

1. Open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. Choose the **ka\-app\-code\-*<username>*** bucket\.

1. Choose **Delete** and then enter the bucket name to confirm deletion\.

## Delete Your IAM Resources<a name="gs-table-cleanup-iam"></a>

Use the following procedure to delete your IAM resources\.

**To delete your IAM resources**

1. Open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. In the navigation bar, choose **Policies**\.

1. In the filter control, enter **kinesis**\.

1. Choose the **kinesis\-analytics\-service\-MyApplication\-*<your\-region>*** policy\.

1. Choose **Policy Actions** and then choose **Delete**\.

1. In the navigation bar, choose **Roles**\.

1. Choose the **kinesis\-analytics\-MyApplication\-*<your\-region>*** role\.

1. Choose **Delete role** and then confirm the deletion\.

## Delete Your CloudWatch Resources<a name="gs-table-cleanup-cw"></a>

Use the following procedure to delete your CloudWatch resources\.

**To delete your CloudWatch resources**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. In the navigation bar, choose **Logs**\.

1. Choose the **/aws/kinesis\-analytics/MyApplication** log group\.

1. Choose **Delete Log Group** and then confirm the deletion\.

## Next Step<a name="gs-table-cleanup-next-step-5"></a>

[Next Steps](gs-table-next-steps.md)