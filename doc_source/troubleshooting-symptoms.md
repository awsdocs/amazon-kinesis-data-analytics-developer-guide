# Application Issues<a name="troubleshooting-symptoms"></a>

This section contains solutions for error conditions that you may encounter with your Kinesis Data Analytics application\.

**Topics**
+ [Application Is Stuck in a Transient Status](#troubleshooting-rt-stuck)
+ [Snapshot Creation Fails](#troubleshooting-rt-snapshots)
+ [Cannot Access Resources in a VPC](#troubleshooting-rt-vpc)
+ [Data Is Lost When Writing to an Amazon S3 Bucket](#troubleshooting-rt-s3)
+ [Application Is in the RUNNING Status But Isn't Processing Data](#troubleshooting-rt-processing)
+ [Snapshot, Application Update, or Application Stop Error: InvalidApplicationConfigurationException](#troubleshooting-rt-appconfigexception)
+ [java\.nio\.file\.NoSuchFileException: /usr/local/openjdk\-8/lib/security/cacerts](#troubleshooting-rt-fnf)

## Application Is Stuck in a Transient Status<a name="troubleshooting-rt-stuck"></a>

If your application stays in a transient status \(`STARTING`, `UPDATING`, `STOPPING`, or `AUTOSCALING`\), you can stop your application by using the [StopApplication](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_StopApplication.html) action with the `Force` parameter set to `true`\. You can't force stop an application in the `DELETING` status\. Alternatively, if the application is in the `UPDATING` or `AUTOSCALING` status, you can roll it back to the previous running version\. When you roll back an application, it loads state data from the last successful snapshot\. If the application has no snapshots, Kinesis Data Analytics rejects the rollback request\. For more information about rolling back an application, see [RollbackApplication](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_RollbackApplication.html) action\.

**Note**  
Force\-stopping your application may lead to data loss or duplication\. To prevent data loss or duplicate processing of data during application restarts, we recommend you to take frequent snapshots of your application\.

Causes for stuck applications include the following:
+ **Application state is too large:** Having an application state that is too large or too persistent can cause the application to become stuck during a checkpoint or snapshot operation\. Check your application's `lastCheckpointDuration` and `lastCheckpointSize` metrics for steadily increasing values or abnormally high values\.
+ **Application code is too large:** Verify that your application JAR file is smaller than 512 MB\. JAR files larger than 512 MB are not supported\.
+ **Application snapshot creation fails:** Kinesis Data Analytics takes a snapshot of the application during an [https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_UpdateApplication.html](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_UpdateApplication.html) or [https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_StopApplication.html](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_StopApplication.html) request\. The service then uses this snapshot state and restores the application using the updated application configuration to provide *exactly\-once* processing semantics\.If automatic snapshot creation fails, see [Snapshot Creation Fails](#troubleshooting-rt-snapshots) following\.
+ **Restoring from a snapshot fails:** If you remove or change an operator in an application update and attempt to restore from a snapshot, the restore will fail by default if the snapshot contains state data for the missing operator\. In addition, the application will be stuck in either the `STOPPED` or `UPDATING` status\. To change this behavior and allow the restore to succeed, change the *AllowNonRestoredState* parameter of the application's [FlinkRunConfiguration](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_FlinkRunConfiguration.html) to `true`\. This will allow the resume operation to skip state data that cannot be mapped to the new program\.

You can check your application status using either the [https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_ListApplications.html](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_ListApplications.html) or the [https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_DescribeApplication.html](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_DescribeApplication.html) actions\.

## Snapshot Creation Fails<a name="troubleshooting-rt-snapshots"></a>

The Kinesis Data Analytics service can't take a snapshot under the following circumstances:
+ The application exceeded the snapshot limit\. The limit for snapshots is 1,000\. For more information, see [Snapshots](how-fault-snapshot.md)\.
+ The application doesn't have permissions to access its source or sink\.
+ The application code isn't functioning properly\.
+ The application is experiencing other configuration issues\.

If you get an exception while taking a snapshot during an application update or while stopping the application, set the `SnapshotsEnabled` property of your application's [https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_ApplicationSnapshotConfiguration.html](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_ApplicationSnapshotConfiguration.html) to `false` and retry the request\. 

Snapshots can fail if your application's operators are not properly provisioned\. For information about tuning operator performance, see [Operator scaling](performance-improving.md#performance-improving-scaling-op)\.

After the application returns to a healthy state, we recommend that you set the application's `SnapshotsEnabled` property to `true`\.

## Cannot Access Resources in a VPC<a name="troubleshooting-rt-vpc"></a>

If your application uses a VPC running on Amazon VPC, do the following to verify that your application has access to its resources:
+ Check your CloudWatch logs for the following error\. This error indicates that your application cannot access resources in your VPC:

  ```
  org.apache.kafka.common.errors.TimeoutException: Failed to update metadata after 60000 ms.
  ```

  If you see this error, verify that your route tables are set up correctly, and that your connectors have the correct connection settings\.

  For information about setting up and analyzing CloudWatch logs, see [Logging and Monitoring](monitoring-overview.md)\.

## Data Is Lost When Writing to an Amazon S3 Bucket<a name="troubleshooting-rt-s3"></a>

Some data loss might occur when writing output to an Amazon S3 bucket using Apache Flink version 1\.6\.2\. We recommend using the latest supported version of Apache Flink when using Amazon S3 for output directly\. To write to an Amazon S3 bucket using Apache Flink 1\.6\.2, we recommend using Kinesis Data Firehose\. For more information about using Kinesis Data Firehose with Kinesis Data Analytics, see [Kinesis Data Firehose Sink](get-started-exercise-fh.md)\.

## Application Is in the RUNNING Status But Isn't Processing Data<a name="troubleshooting-rt-processing"></a>

You can check your application status by using either the [https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_ListApplications.html](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_ListApplications.html) or the [https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_DescribeApplication.html](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_DescribeApplication.html) actions\. If your application enters the `RUNNING` status but isn't writing data to your sink, you can troubleshoot the issue by adding an Amazon CloudWatch log stream to your application\. For more information, see [Working with Application CloudWatch Logging Options](cloudwatch-logs.md#adding_cloudwatch)\. The log stream contains messages that you can use to troubleshoot application issues\.

## Snapshot, Application Update, or Application Stop Error: InvalidApplicationConfigurationException<a name="troubleshooting-rt-appconfigexception"></a>

An error similar to the following might occur during a snapshot operation, or during an operation that creates a snapshot, such as updating or stopping an application:

```
An error occurred (InvalidApplicationConfigurationException) when calling the UpdateApplication operation: 

Failed to take snapshot for the application xxxx at this moment. The application is currently experiencing downtime. 
Please check the application's CloudWatch metrics or CloudWatch logs for any possible errors and retry the request. 
You can also retry the request after disabling the snapshots in the Kinesis Data Analytics console or by updating 
the ApplicationSnapshotConfiguration through the AWS SDK
```

This error occurs when the application is unable to create a snapshot\. 

If you encounter this error during a snapshot operation or an operation that creates a snapshot, do the following:
+ Disable snapshots for your application\. You can do this either in the Kinesis Data Analytics console, or by using the `SnapshotsEnabledUpdate` parameter of the [UpdateApplication](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_UpdateApplication.html) action\.
+ Investigate why snapshots cannot be created\. For more information, see [Application Is Stuck in a Transient Status](#troubleshooting-rt-stuck)\.
+ Reenable snapshots when the application returns to a healthy state\.

## java\.nio\.file\.NoSuchFileException: /usr/local/openjdk\-8/lib/security/cacerts<a name="troubleshooting-rt-fnf"></a>

The location of the SSL truststore was updated in a previous deployment\. Use the following value for the `ssl.truststore.location` parameter instead:

```
/usr/lib/jvm/java-11-amazon-corretto/lib/security/cacerts
```