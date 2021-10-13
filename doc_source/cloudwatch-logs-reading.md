# Analyzing Logs with CloudWatch Logs Insights<a name="cloudwatch-logs-reading"></a>

After you've added a CloudWatch logging option to your application as described in the previous section, you can use CloudWatch Logs Insights to query your log streams for specific events or errors\.

CloudWatch Logs Insights enables you to interactively search and analyze your log data in CloudWatch Logs\. 

For information on getting started with CloudWatch Logs Insights, see [Analyze Log Data with CloudWatch Logs Insights](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/AnalyzingLogData.html)\.

## Run a Sample Query<a name="cloudwatch-logs-reading-run"></a>

This section describes how to run a sample CloudWatch Logs Insights query\.

**Prerequisites**
+ Existing log groups and log streams set up in CloudWatch Logs\.
+ Existing logs stored in CloudWatch Logs\.

If you use services such as AWS CloudTrail, Amazon Route 53, or Amazon VPC, you've probably already set up logs from those services to go to CloudWatch Logs\. For more information about sending logs to CloudWatch Logs, see [Getting Started with CloudWatch Logs](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/CWL_GettingStarted.html)\.

Queries in CloudWatch Logs Insights return either a set of fields from log events, or the result of a mathematical aggregation or other operation performed on log events\. This section demonstrates a query that returns a list of log events\.

**To run a CloudWatch Logs Insights sample query**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. In the navigation pane, choose **Insights**\.

   

1. The query editor near the top of the screen contains a default query that returns the 20 most recent log events\. Above the query editor, select a log group to query\.

   

   When you select a log group, CloudWatch Logs Insights automatically detects fields in the data in the log group and displays them in **Discovered fields** in the right pane\. It also displays a bar graph of log events in this log group over time\. This bar graph shows the distribution of events in the log group that matches your query and time range, not just the events displayed in the table\.

1. Choose **Run query**\.

   The results of the query appear\. In this example, the results are the most recent 20 log events of any type\.

1. To see all of the fields for one of the returned log events, choose the arrow to the left of that log event\.

For more information about how to run and modify CloudWatch Logs Insights queries, see [Run and Modify a Sample Query](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/CWL_AnalyzeLogData_RunSampleQuery.html)\.

## Example Queries<a name="cloudwatch-logs-reading-examples"></a>

This section contains CloudWatch Logs Insights example queries for analyzing Kinesis Data Analytics application logs\. These queries search for several example error conditions, and serve as templates for writing queries that find other error conditions\. 

**Note**  
Replace the Region \(*us\-west\-2*\), Account ID \(*012345678901*\) and application name \(*YourApplication*\) in the following query examples with your application's Region and your Account ID\.

**Topics**
+ [Analyze Operations: Distribution of Tasks](#cloudwatch-logs-reading-tm)
+ [Analyze Operations: Change in Parallelism](#cloudwatch-logs-reading-auto)
+ [Analyze Errors: Access Denied](#cloudwatch-logs-reading-access)
+ [Analyze Errors: Source or Sink Not Found](#cloudwatch-logs-reading-con)
+ [Analyze Errors: Application Task\-Related Failures](#cloudwatch-logs-reading-apps)

### Analyze Operations: Distribution of Tasks<a name="cloudwatch-logs-reading-tm"></a>

The following CloudWatch Logs Insights query returns the number of tasks the Apache Flink Job Manager distributes between Task Managers\. You need to set the query's time frame to match one job run so that the query doesn't return tasks from previous jobs\. For more information about Parallelism, see [Scaling](how-scaling.md)\. 

```
fields @timestamp, message
| filter message like /Deploying/
| parse message " to flink-taskmanager-*" as @tmid
| stats count(*) by @tmid
| sort @timestamp desc
| limit 2000
```

The following CloudWatch Logs Insights query returns the subtasks assigned to each Task Manager\. The total number of subtasks is the sum of every task's parallelism\. Task parallelism is derived from operator parallelism, and is the same as the application's parallelism by default, unless you change it in code by specifying `setParallelism`\. For more information about setting operator parallelism, see [ Setting the Parallelism: Operator Level](https://ci.apache.org/projects/flink/flink-docs-release-1.11/dev/parallel.html#operator-level) in the [Apache Flink documentation](https://ci.apache.org/projects/flink/flink-docs-release-1.11/)\.

```
fields @timestamp, @tmid, @subtask
| filter message like /Deploying/
| parse message "Deploying * to flink-taskmanager-*" as @subtask, @tmid
| sort @timestamp desc
| limit 2000
```

For more information about task scheduling, see [Jobs and Scheduling](https://ci.apache.org/projects/flink/flink-docs-release-1.11/internals/job_scheduling.html) in the [Apache Flink documentation](https://ci.apache.org/projects/flink/flink-docs-release-1.11/)\.

### Analyze Operations: Change in Parallelism<a name="cloudwatch-logs-reading-auto"></a>

The following CloudWatch Logs Insights query returns changes to an application's parallelism \(for example, due to automatic scaling\)\. This query also returns manual changes to the application's parallelism\. For more information about automatic scaling, see [Automatic Scaling](how-scaling.md#how-scaling-auto)\.

```
fields @timestamp, @parallelism
| filter message like /property: parallelism.default, /
| parse message "default, *" as @parallelism
| sort @timestamp asc
```

### Analyze Errors: Access Denied<a name="cloudwatch-logs-reading-access"></a>

The following CloudWatch Logs Insights query returns `Access Denied` logs\.

```
fields @timestamp, @message, @messageType
| filter applicationARN like /arn:aws:kinesisanalytics:us-west-2:012345678901:application\/YourApplication/
| filter @message like /AccessDenied/
| sort @timestamp desc
```

### Analyze Errors: Source or Sink Not Found<a name="cloudwatch-logs-reading-con"></a>

The following CloudWatch Logs Insights query returns `ResourceNotFound` logs\. `ResourceNotFound` logs result if a Kinesis source or sink is not found\.

```
fields @timestamp,@message
| filter applicationARN like /arn:aws:kinesisanalytics:us-west-2:012345678901:application\/YourApplication/
| filter @message like /ResourceNotFoundException/
| sort @timestamp desc
```

### Analyze Errors: Application Task\-Related Failures<a name="cloudwatch-logs-reading-apps"></a>

The following CloudWatch Logs Insights query returns an application's task\-related failure logs\. These logs result if an application's status switches from `RUNNING` to `RESTARTING`\.

```
fields @timestamp,@message
| filter applicationARN like /arn:aws:kinesisanalytics:us-west-2:012345678901:application\/YourApplication/
| filter @message like /switched from RUNNING to RESTARTING/
| sort @timestamp desc
```

For applications using Apache Flink version 1\.8\.2 and prior, task\-related failures will result in the application status switching from `RUNNING` to `FAILED` instead\. When using Apache Flink 1\.8\.2 and prior, use the following query to search for application task\-related failures:

```
fields @timestamp,@message
| filter applicationARN like /arn:aws:kinesisanalytics:us-west-2:012345678901:application\/YourApplication/
| filter @message like /switched from RUNNING to FAILED/
| sort @timestamp desc
```