# Creating CloudWatch Alarms to Monitor Amazon Kinesis Data Analytics<a name="creating-alarms"></a>

You can create an Amazon CloudWatch alarm that sends an Amazon SNS message when the alarm changes state\. An alarm watches a single metric over a time period you specify\. It performs one or more actions based on the value of the metric relative to a given threshold over a number of time periods\. The action is a notification sent to an Amazon SNS topic or Auto Scaling policy\. 

Alarms invoke actions for sustained state changes only\. For a CloudWatch alarm to invoke an action, the state must have changed and been maintained for a specified amount of time\.

You can set alarms using the AWS Management Console, CloudWatch AWS CLI, or CloudWatch API, as described following\.

**To set an alarm using the CloudWatch console**

1. Sign in to the AWS Management Console and open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1.  Choose **Create Alarm**\. The **Create Alarm Wizard** starts\. 

1. Choose **Kinesis Analytics Metrics**\. Then scroll through the Amazon Kinesis Data Analytics metrics to locate the metric you want to place an alarm on\. 

   To display just Amazon Kinesis Data Analytics metrics, search for the file system ID of your file system\. Choose the metric to create an alarm for, and then choose **Next**\.

1. Enter values for **Name**, **Description**, and **Whenever** for the metric\. 

1. If you want CloudWatch to send you an email when the alarm state is reached, in the **Whenever this alarm:** field, choose **State is ALARM**\. In the **Send notification to:** field, choose an existing SNS topic\. If you select **Create topic**, you can set the name and email addresses for a new email subscription list\. This list is saved and appears in the field for future alarms\. 
**Note**  
 If you use **Create topic** to create a new Amazon SNS topic, the email addresses must be verified before they receive notifications\. Emails are only sent when the alarm enters an alarm state\. If this alarm state change happens before the email addresses are verified, they do not receive a notification\.

1. In the **Alarm Preview** section, preview the alarm you’re about to create\. 

1. Choose **Create Alarm** to create the alarm\.

**To set an alarm using the CloudWatch CLI**
+  Call `[mon\-put\-metric\-alarm](https://docs.aws.amazon.com/AmazonCloudWatch/latest/cli/cli-mon-put-metric-alarm.html)`\. For more information, see the [Amazon CloudWatch CLI Reference](https://docs.aws.amazon.com/AmazonCloudWatch/latest/cli/)\. 

**To set an alarm using the CloudWatch API**
+ Call `[PutMetricAlarm](https://docs.aws.amazon.com/AmazonCloudWatch/latest/APIReference/API_PutMetricAlarm.html)`\. For more information, see the [Amazon CloudWatch API Reference](https://docs.aws.amazon.com/AmazonCloudWatch/latest/APIReference/)\. 