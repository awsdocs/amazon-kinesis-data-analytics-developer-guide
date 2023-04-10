# Step 4: Clean Up AWS Resources<a name="getting-started-cleanup"></a>

This section includes procedures for cleaning up AWS resources created in the Getting Started tutorial\.

**Topics**
+ [Delete Your Kinesis Data Analytics Application](#getting-started-cleanup-app)
+ [Delete Your Kinesis Data Streams](#getting-started-cleanup-stream)
+ [Delete Your Amazon S3 Object and Bucket](#getting-started-cleanup-s3)
+ [Delete Your IAM Resources](#getting-started-cleanup-iam)
+ [Delete Your CloudWatch Resources](#getting-started-cleanup-cw)
+ [Next Step](#getting-started-cleanup-next-step-5)

## Delete Your Kinesis Data Analytics Application<a name="getting-started-cleanup-app"></a>

1. Open the Kinesis console at [https://console\.aws\.amazon\.com/kinesis](https://console.aws.amazon.com/kinesis)\.

1. In the Kinesis Data Analytics panel, choose **MyApplication**\.

1. In the application's page, choose **Delete** and then confirm the deletion\.

## Delete Your Kinesis Data Streams<a name="getting-started-cleanup-stream"></a>

1. Open the Kinesis Data Analytics console at [ https://console\.aws\.amazon\.com/kinesisanalytics](https://console.aws.amazon.com/kinesisanalytics)\.

1. In the Kinesis Data Streams panel, choose **ExampleInputStream**\.

1. In the **ExampleInputStream** page, choose **Delete Kinesis Stream** and then confirm the deletion\.

1. In the **Kinesis streams** page, choose the **ExampleOutputStream**, choose **Actions**, choose **Delete**, and then confirm the deletion\.

## Delete Your Amazon S3 Object and Bucket<a name="getting-started-cleanup-s3"></a>

1. Open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. Choose the **ka\-app\-code\-*<username>* bucket\.**

1. Choose **Delete** and then enter the bucket name to confirm deletion\.

## Delete Your IAM Resources<a name="getting-started-cleanup-iam"></a>

1. Open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. In the navigation bar, choose **Policies**\.

1. In the filter control, enter **kinesis**\.

1. Choose the **kinesis\-analytics\-service\-MyApplication\-*<your\-region>*** policy\.

1. Choose **Policy Actions** and then choose **Delete**\.

1. In the navigation bar, choose **Roles**\.

1. Choose the **kinesis\-analytics\-MyApplication\-*<your\-region>*** role\.

1. Choose **Delete role** and then confirm the deletion\.

## Delete Your CloudWatch Resources<a name="getting-started-cleanup-cw"></a>

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. In the navigation bar, choose **Logs**\.

1. Choose the **/aws/kinesis\-analytics/MyApplication** log group\.

1. Choose **Delete Log Group** and then confirm the deletion\.

## Next Step<a name="getting-started-cleanup-next-step-5"></a>

[Step 5: Next Steps](getting-started-next-steps.md)