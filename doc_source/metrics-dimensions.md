# Viewing Amazon Kinesis Data Analytics Metrics and Dimensions<a name="metrics-dimensions"></a>

When your Amazon Kinesis Data Analytics application processes data streams, Kinesis Data Analytics sends the following metrics and dimensions to CloudWatch\. You can use the following procedures to view the metrics for Kinesis Data Analytics\.

On the console, metrics are grouped first by service namespace, and then by the dimension combinations within each namespace\.

**To view metrics using the CloudWatch console**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. In the navigation pane, choose **Metrics**\.

1. In the **CloudWatch Metrics by Category** pane for Kinesis Data Analytics, choose a metrics category\.

1. In the upper pane, scroll to view the full list of metrics\.

**To view metrics using the AWS CLI**
+ At a command prompt, use the following command\.

  ```
  1. aws cloudwatch list-metrics --namespace "AWS/KinesisAnalytics" --region region
  ```

 Kinesis Data Analytics metrics are collected at the following levels:
+ Application 
+ Input stream
+ Output stream