# Running a Kinesis Data Analytics for Apache Flink Application<a name="how-running-apps"></a>

This topic contains information about running a Kinesis Data Analytics for Apache Flink application\.

When you run your Kinesis Data Analytics application, the Kinesis Data Analytics service creates an Apache Flink job\. An Apache Flink job is the execution lifecycle of your Kinesis Data Analytics application\. The execution of the job, and the resources it uses, are managed by the Job Manager\. The Job Manager separates the execution of the application into tasks\. Each task is managed by a Task Manager\. When you monitor your application's performance, you can examine the performance of each Task Manager, or of the Job Manager as a whole\. 

For information about Apache Flink jobs, see [Jobs and Scheduling](https://ci.apache.org/projects/flink/flink-docs-release-1.11/internals/job_scheduling.html) in the *[Apache Flink Documentation](https://ci.apache.org/projects/flink/flink-docs-release-1.11/)\.*

## Application and Job Status<a name="how-running-job-status"></a>

Both your application and the application's job have a current execution status:
+ **Application status:** Your application has a current status that describes its phase of execution\. Application statuses include the following:
  + **Steady application statuses:** Your application typically stays in these statuses until you make a status change:
    + **READY:** A new or stopped application is in the READY status until you run it\.
    + **RUNNING:** An application that has successfully started is in the RUNNING status\.
  + **Transient application statuses:** An application in these statuses is typically in the process of transitioning to another status\. If an application stays in a transient status for a length of time, you can stop the application using the [StopApplication](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_StopApplication.html) action with the `Force` parameter set to `true`\. These statuses include the following:
    + `STARTING:` Occurs after the [StartApplication](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_StartApplication.html) action\. The application is transitioning from the `READY` to the `RUNNING` status\.
    + **STOPPING:** Occurs after the [StopApplication](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_StopApplication.html) action\. The application is transitioning from the `RUNNING` to the `READY` status\.
    + `DELETING:` Occurs after the [DeleteApplication](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_DeleteApplication.html) action\. The application is in the process of being deleted\.
    + `UPDATING:` Occurs after the [UpdateApplication](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_UpdateApplication.html) action\. The application is updating, and will transition back to the `RUNNING` or `READY` status\.
    + `AUTOSCALING:` The application has the `AutoScalingEnabled` property of the [ ParallelismConfiguration](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_ParallelismConfiguration.html) set to `true`, and the service is increasing the parallelism of the application\. When the application is in this status, the only valid API action you can use is the [StopApplication](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_StopApplication.html) action with the `Force` parameter set to `true`\. For information about automatic scaling, see [Automatic Scaling](how-scaling.md#how-scaling-auto)\.
    + `FORCE_STOPPING:` Occurs after the [StopApplication](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_StopApplication.html) action is called with the `Force` parameter set to `true`\. The application is in the process of being force stopped\. The application transitions from the `STARTING`, `UPDATING`, `STOPPING`, or `AUTOSCALING` status to the `READY` status\.
    + `ROLLING_BACK:` Occurs after the [RollbackApplication](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_RollbackApplication.html) action is called\. The application is in the process of being rolled back to a previous version\. The application transitions from the `UPDATING` or `AUTOSCALING` status to the `RUNNING` status\.
    + `ROLLED_BACK:` When you successfully roll back an application, this becomes the status of the version that you rolled back from\. For information about rolling back an application, see [RollbackApplication](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_RollbackApplication.html)\.
    + `MAINTENANCE:` Occurs while Kinesis Data Analytics applies patches to your application\. For more information, see [Kinesis Data Analytics for Apache Flink Maintenance](maintenance.md)\.

  You can check your application's status using the console, or by using the [DescribeApplication](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_DescribeApplication.html) action\.
+ **Job status:** When your application is in the `RUNNING` status, your job has a status that describes its current execution phase\. A job starts in the `CREATED` status, and then proceeds to the `RUNNING` status when it has started\. If error conditions occur, your application enters the following status: 
  + For applications using Apache Flink 1\.11 and later, your application enters the `RESTARTING` status\.
  + For applications using Apache Flink 1\.8 and prior, your application enters the `FAILING` status\.

  The application then proceeds to either the `RESTARTING` or `FAILED` status, depending on whether the job can be restarted\. 

  You can check the job's status by examining your application's CloudWatch log for status changes\.

## Batch workloads<a name="batch-workloads"></a>

Kinesis Data Analytics supports running Apache Flink batch workloads\. In a batch job, when an Apache Flink job gets to the **FINISHED** status, Kinesis Data Analytics application status is set to **READY**\. For more information about Flink job statuses, see [Jobs and Scheduling](https://nightlies.apache.org/flink/flink-docs-master/docs/internals/job_scheduling/)\.