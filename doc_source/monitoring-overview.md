# Monitoring Amazon Kinesis Data Analytics for SQL Applications<a name="monitoring-overview"></a>

Monitoring is an important part of maintaining the reliability, availability, and performance of Amazon Kinesis Data Analytics and your Amazon Kinesis Data Analytics application\. You should collect monitoring data from all of the parts of your AWS solution so that you can more easily debug a multipoint failure if one occurs\. Before you start monitoring Amazon Kinesis Data Analytics, however, you should create a monitoring plan that includes answers to the following questions:
+ What are your monitoring goals?
+ What resources will you monitor?
+ How often will you monitor these resources?
+ What monitoring tools will you use?
+ Who will perform the monitoring tasks?
+ Who should be notified when something goes wrong?

The next step is to establish a baseline for normal Amazon Kinesis Data Analytics performance in your environment, by measuring performance at various times and under different load conditions\. As you monitor Amazon Kinesis Data Analytics, you can store historical monitoring data\. If you do, you can compare it with current performance data, identify normal performance patterns and performance anomalies, and devise methods to address issues\.

With Amazon Kinesis Data Analytics, you monitor the application\. The application processes data streams \(input or output\), both of which include *identifiers* which you can use to narrow your search on CloudWatch logs\. For information about how Amazon Kinesis Data Analytics processes data streams, see [Amazon Kinesis Data Analytics for SQL Applications: How It Works](how-it-works.md)\.

The most important metric is the `millisBehindLatest`, which indicates how far behind an application is reading from the streaming source\. In a typical case, the milliseconds behind should be at or near zero\. It is common for brief spikes to appear, which appears as an increase in `millisBehindLatest`\.

We recommend that you set up a CloudWatch alarm that triggers when the application is behind by more than an hour reading the streaming source\. For some use cases that require very close to real\-time processing, such as emitting processed data to a live application, you might choose to set the alarm at a lower value, such as five minutes\.

**Topics**
+ [Monitoring Tools](monitoring-automated-manual.md)
+ [Monitoring with Amazon CloudWatch](monitoring-cloudwatch.md)
+ [Logging Kinesis Data Analytics API Calls with AWS CloudTrail](logging-using-cloudtrail.md)