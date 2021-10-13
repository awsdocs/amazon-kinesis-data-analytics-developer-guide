# Logging and Monitoring in Amazon Kinesis Data Analytics for Apache Flink<a name="monitoring-overview"></a>

Monitoring is an important part of maintaining the reliability, availability, and performance of Amazon Kinesis Data Analytics and your Kinesis Data Analytics applications\. You should collect monitoring data from all of the parts of your AWS solution so that you can more easily debug a multipoint failure if one occurs\. 

Before you start monitoring Kinesis Data Analytics, you should create a monitoring plan that includes answers to the following questions:
+ What are your monitoring goals?
+ What resources will you monitor?
+ How often will you monitor these resources?
+ What monitoring tools will you use?
+ Who will perform the monitoring tasks?
+ Who should be notified when something goes wrong?

The next step is to establish a baseline for normal Kinesis Data Analytics performance in your environment\. You do this by measuring performance at various times and under different load conditions\. As you monitor Kinesis Data Analytics, you can store historical monitoring data\. You can then compare it with current performance data, identify normal performance patterns and performance anomalies, and devise methods to address issues\.

**Topics**
+ [Setting Up Application Logging](cloudwatch-logs.md)
+ [Analyzing Logs with CloudWatch Logs Insights](cloudwatch-logs-reading.md)
+ [Viewing Kinesis Data Analytics Metrics and Dimensions](metrics-dimensions.md)
+ [Writing Custom Messages to CloudWatch Logs](cloudwatch-logs-writing.md)
+ [Logging Kinesis Data Analytics API Calls with AWS CloudTrail](logging-using-cloudtrail.md)