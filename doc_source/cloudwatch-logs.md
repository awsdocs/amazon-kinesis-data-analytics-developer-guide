# Setting Up Application Logging<a name="cloudwatch-logs"></a>

By adding an Amazon CloudWatch logging option to your Kinesis Data Analytics application, you can monitor for application events or configuration problems\.

This topic describes how to configure your application to write application events to a CloudWatch Logs stream\. A CloudWatch logging option is a collection of application settings and permissions that your application uses to configure the way it writes application events to CloudWatch Logs\. You can add and configure a CloudWatch logging option using either the AWS Management Console or the AWS Command Line Interface \(AWS CLI\)\.

Note the following about adding a CloudWatch logging option to your application:
+ When you add a CloudWatch logging option using the console, Kinesis Data Analytics creates the CloudWatch log group and log stream for you and adds the permissions your application needs to write to the log stream\. 
+ When you add a CloudWatch logging option using the API, you must also create the application's log group and log stream, and add the permissions your application needs to write to the log stream\.

**Topics**
+ [Setting Up CloudWatch Logging Using the Console](#cloudwatch-logs-console)
+ [Setting Up CloudWatch Logging Using the CLI](#cloudwatch-logs-api)
+ [Application Monitoring Levels](#cloudwatch_levels)
+ [Logging Best Practices](#cloudwatch_bestpractices)
+ [Logging Troubleshooting](#cloudwatch_troubleshooting)
+ [Next Step](#cloudwatch_next)

## Setting Up CloudWatch Logging Using the Console<a name="cloudwatch-logs-console"></a>

When you enable CloudWatch logging for your application in the console, a CloudWatch log group and log stream is created for you\. Also, your application's permissions policy is updated with permissions to write to the stream\. 

The following screenshot shows the **CloudWatch logging** setting in the **Configure application** page\.

 

![\[Console screenshot showing the settings on the create application page.\]](http://docs.aws.amazon.com/kinesisanalytics/latest/java/images/logging.png)

Kinesis Data Analytics creates a log group named using the following convention, where *ApplicationName* is your application's name\.

```
/aws/kinesis-analytics/ApplicationName
```

Kinesis Data Analytics creates a log stream in the new log group with the following name\.

```
kinesis-analytics-log-stream
```

You set the application monitoring metrics level and monitoring log level using the **Monitoring log level** section of the **Configure application** page\. For information about application log levels, see [Application Monitoring Levels](#cloudwatch_levels)\.

## Setting Up CloudWatch Logging Using the CLI<a name="cloudwatch-logs-api"></a>

To add a CloudWatch logging option using the AWS CLI, do the following: 
+ Create a CloudWatch log group and log stream\.
+ Add a logging option when you create an application by using the [CreateApplication](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_CreateApplication.html) action, or add a logging option to an existing application using the [AddApplicationCloudWatchLoggingOption](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_AddApplicationCloudWatchLoggingOption.html) action\.
+ Add permissions to your application's policy to write to the logs\.

**Topics**
+ [Creating a CloudWatch Log Group and Log Stream](#cloudwatch-logs-api-create)
+ [Working with Application CloudWatch Logging Options](#adding_cloudwatch)
+ [Adding Permissions to Write to the CloudWatch Log Stream](#enable_putlogevents)

### Creating a CloudWatch Log Group and Log Stream<a name="cloudwatch-logs-api-create"></a>

You create a CloudWatch log group and stream using either the CloudWatch Logs console or the API\. For information about creating a CloudWatch log group and log stream, see [Working with Log Groups and Log Streams](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/Working-with-log-groups-and-streams.html)\.

### Working with Application CloudWatch Logging Options<a name="adding_cloudwatch"></a>

Use the following API actions to add a CloudWatch log option to a new or existing application or change a log option for an existing application\. For information about how to use a JSON file for input for an API action, see [Kinesis Data Analytics API Example Code](api-examples.md)\.

#### Adding a CloudWatch Log Option When Creating an Application<a name="add_cloudwatch_create"></a>

The following example demonstrates how to use the `CreateApplication` action to add a CloudWatch log option when you create an application\. In the example, replace *Amazon Resource Name \(ARN\) of the CloudWatch Log stream to add to the new application* with your own information\. For more information about the action, see [https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_CreateApplication.html](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_CreateApplication.html)\.

```
{
    "ApplicationName": "test",
    "ApplicationDescription": "test-application-description",
    "RuntimeEnvironment": "FLINK-1_13",
    "ServiceExecutionRole": "arn:aws:iam::123456789123:role/myrole",
    "ApplicationConfiguration": {
        "ApplicationCodeConfiguration": {
            "CodeContent": {
                "S3ContentLocation":{
                              "BucketARN": "arn:aws:s3:::mybucket",
                              "FileKey": "myflink.jar"
                }
            },
            "CodeContentType": "ZIPFILE"
        }
    },
    "CloudWatchLoggingOptions": [{
      "LogStreamARN": "<Amazon Resource Name (ARN) of the CloudWatch log stream to add to the new application>"
	}]
}
```

#### Adding a CloudWatch Log Option to an Existing Application<a name="add_to_existing_app"></a>

The following example demonstrates how to use the `AddApplicationCloudWatchLoggingOption` action to add a CloudWatch log option to an existing application\. In the example, replace each *user input placeholder* with your own information\. For more information about the action, see [https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_AddApplicationCloudWatchLoggingOption.html](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_AddApplicationCloudWatchLoggingOption.html)\.

```
{
   "ApplicationName": "<Name of the application to add the log option to>",
   "CloudWatchLoggingOption": { 
      "LogStreamARN": "<ARN of the log stream to add to the application>"
   },
   "CurrentApplicationVersionId": <Version of the application to add the log to>
}
```

#### Updating an Existing CloudWatch Log Option<a name="update_existing"></a>

The following example demonstrates how to use the `UpdateApplication` action to modify an existing CloudWatch log option\. In the example, replace each *user input placeholder* with your own information\. For more information about the action, see [https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_UpdateApplication.html](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_UpdateApplication.html)\.

```
{
   "ApplicationName": "<Name of the application to update the log option for>",
   "CloudWatchLoggingOptionUpdates": [ 
         { 
            "CloudWatchLoggingOptionId": "<ID of the logging option to modify>",
            "LogStreamARNUpdate": "<ARN of the new log stream to use>"
         }
      ],
   "CurrentApplicationVersionId": <ID of the application version to modify>
}
```

#### Deleting a CloudWatch Log Option from an Application<a name="delete-log"></a>

The following example demonstrates how to use the `DeleteApplicationCloudWatchLoggingOption` action to delete an existing CloudWatch log option\. In the example, replace each *user input placeholder* with your own information\. For more information about the action, see [https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_DeleteApplicationCloudWatchLoggingOption.html](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_DeleteApplicationCloudWatchLoggingOption.html)\.

```
{
   "ApplicationName": "<Name of application to delete log option from>",
   "CloudWatchLoggingOptionId": "<ID of the application log option to delete>",
   "CurrentApplicationVersionId": <Version of the application to delete the log option from>
}
```

#### Setting the Application Logging Level<a name="cloudwatch-level"></a>

To set the level of application logging, use the [https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_MonitoringConfiguration.html](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_MonitoringConfiguration.html) parameter of the [https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_CreateApplication.html](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_CreateApplication.html) action or the [https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_MonitoringConfigurationUpdate.html](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_MonitoringConfigurationUpdate.html) parameter of the [https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_UpdateApplication.html](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_UpdateApplication.html) action\. 

For information about application log levels, see [Application Monitoring Levels](#cloudwatch_levels)\.

##### Set the Application Logging Level when Creating an Application<a name="cloudwatch-level-create"></a>

The following example request for the [https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_CreateApplication.html](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_CreateApplication.html) action sets the application log level to `INFO`\.

```
{
   "ApplicationName": "MyApplication",                    
   "ApplicationDescription": "My Application Description",
   "ApplicationConfiguration": {
      "ApplicationCodeConfiguration":{
      "CodeContent":{
        "S3ContentLocation":{
          "BucketARN":"arn:aws:s3:::mybucket",
          "FileKey":"myflink.jar",
          "ObjectVersion":"AbCdEfGhIjKlMnOpQrStUvWxYz12345"
        }
      },
      "CodeContentType":"ZIPFILE"
      },
      "FlinkApplicationConfiguration": 
         "MonitoringConfiguration": { 
            "ConfigurationType": "CUSTOM",
            "LogLevel": "INFO"
         }
      },
   "RuntimeEnvironment": "FLINK-1_13",
   "ServiceExecutionRole": "arn:aws:iam::123456789123:role/myrole"
}
```

##### Update the Application Logging Level<a name="cloudwatch-level-update"></a>

The following example request for the [https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_UpdateApplication.html](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_UpdateApplication.html) action sets the application log level to `INFO`\.

```
{
   "ApplicationConfigurationUpdate": {
      "FlinkApplicationConfigurationUpdate": { 
         "MonitoringConfigurationUpdate": { 
            "ConfigurationTypeUpdate": "CUSTOM",
            "LogLevelUpdate": "INFO"
         }
      }
   }
}
```

### Adding Permissions to Write to the CloudWatch Log Stream<a name="enable_putlogevents"></a>

Kinesis Data Analytics needs permissions to write misconfiguration errors to CloudWatch\. You can add these permissions to the AWS Identity and Access Management \(IAM\) role that Kinesis Data Analytics assumes\.

For more information about using an IAM role for Kinesis Data Analytics, see [Identity and Access Management in Amazon Kinesis Data Analytics for Apache Flink](how-iam.md)\.

#### Trust Policy<a name="enable_putlogevents_trust_policy"></a>

To grant Kinesis Data Analytics permissions to assume an IAM role, you can attach the following trust policy to the service execution role\.

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "Service": "kinesisanalytics.amazonaws.com"
      },
      "Action": "sts:AssumeRole"
    }
  ]
}
```

#### Permissions Policy<a name="enable_putlogevents_permissions_policy"></a>

To grant permissions to an application to write log events to CloudWatch from a Kinesis Data Analytics resource, you can use the following IAM permissions policy\. Provide the correct Amazon Resource Names \(ARNs\) for your log group and stream\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "Stmt0123456789000",
            "Effect": "Allow",
            "Action": [
                "logs:PutLogEvents",
                "logs:DescribeLogGroups",
                "logs:DescribeLogStreams"
            ],
            "Resource": [
                "arn:aws:logs:us-east-1:123456789012:log-group:my-log-group:log-stream:my-log-stream*",
                "arn:aws:logs:us-east-1:123456789012:log-group:my-log-group:*",
                "arn:aws:logs:us-east-1:123456789012:log-group:*",
            ]
        }
    ]
}
```

## Application Monitoring Levels<a name="cloudwatch_levels"></a>

You control the generation of application log messages using the application's *Monitoring Metrics Level* and *Monitoring Log Level*\.

The application's monitoring metrics level controls the granularity of log messages\. Monitoring metrics levels are defined as follows:
+ **Application**: Metrics are scoped to the entire application\.
+ **Task**: Metrics are scoped to each task\. For information about tasks, see [Application Scaling in Kinesis Data Analytics for Apache Flink](how-scaling.md)\.
+ **Operator**: Metrics are scoped to each operator\. For information about operators, see [Transforming Data Using Operators in Kinesis Data Analytics for Apache Flink With the DataStream API](how-operators.md)\.
+ **Parallelism**: Metrics are scoped to application parallelism\. You can only set this metrics level using the [ MonitoringConfigurationUpdate](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_MonitoringConfigurationUpdate.html) parameter of the [ UpdateApplication](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_UpdateApplication.html) API\. You cannot set this metrics level using the console\. For information about parallelism, see [Application Scaling in Kinesis Data Analytics for Apache Flink](how-scaling.md)\.

The application's monitoring log level controls the verbosity of the application's log\. Monitoring log levels are defined as follows:
+ **Error**: Potential catastrophic events of the application\.
+ **Warn**: Potentially harmful situations of the application\.
+ **Info**: Informational and transient failure events of the application\. We recommend that you use this logging level\.
+ **Debug**: Fine\-grained informational events that are most useful to debug an application\. *Note*: Only use this level for temporary debugging purposes\. 

## Logging Best Practices<a name="cloudwatch_bestpractices"></a>

We recommend that your application use the **Info** logging level\. We recommend this level to ensure that you see Apache Flink errors, which are logged at the **Info** level rather than the **Error** level\.

We recommend that you use the **Debug** level only temporarily while investigating application issues\. Switch back to the **Info** level when the issue is resolved\. Using the **Debug** logging level will significantly affect your application's performance\.

Excessive logging can also significantly impact application performance\. We recommend that you do not write a log entry for every record processed, for example\. Excessive logging can cause severe bottlenecks in data processing and can lead to back pressure in reading data from the sources\.

## Logging Troubleshooting<a name="cloudwatch_troubleshooting"></a>

If application logs are not being written to the log stream, verify the following: 
+ Verify that your application's IAM role and policies are correct\. Your application's policy needs the following permissions to access your log stream:
  + `logs:PutLogEvents`
  + `logs:DescribeLogGroups`
  + `logs:DescribeLogStreams`

  For more information, see [Adding Permissions to Write to the CloudWatch Log Stream](#enable_putlogevents)\.
+ Verify that your application is running\. To check your application's status, view your application's page in the console, or use the [DescribeApplication](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_DescribeApplication.html) or [ListApplications](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_ListApplications.html) actions\.
+ Monitor CloudWatch metrics such as `downtime` to diagnose other application issues\. For information about reading CloudWatch metrics, see [Metrics and Dimensions](metrics-dimensions.md)\.

## Next Step<a name="cloudwatch_next"></a>

After you have enabled CloudWatch logging in your application, you can use CloudWatch Logs Insights to analyze your application logs\. For more information, see [Analyzing Logs with CloudWatch Logs Insights](cloudwatch-logs-reading.md)\.