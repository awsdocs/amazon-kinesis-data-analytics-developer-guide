# Implementing Fault Tolerance in Kinesis Data Analytics for Apache Flink<a name="how-fault"></a>

Checkpointing is the method that is used for implementing fault tolerance in Amazon Kinesis Data Analytics for Apache Flink\. A *checkpoint* is an up\-to\-date backup of a running application that is used to recover immediately from an unexpected application disruption or failover\. 

For details on checkpointing in Apache Flink applications, see [Checkpoints](https://nightlies.apache.org/flink/flink-docs-release-1.15/ops/state/checkpoints.html) in the [Apache Flink Documentation](https://nightlies.apache.org/flink/flink-docs-release-1.15/)\.

A *snapshot* is a manually created and managed backup of application state\. Snapshots let you restore your application to a previous state by calling [https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_UpdateApplication.html](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_UpdateApplication.html)\. For more information, see [Managing Application Backups Using Snapshots](how-fault-snapshot.md)\.

If checkpointing is enabled for your application, then the service provides fault tolerance by creating and loading backups of application data in the event of unexpected application restarts\. These unexpected application restarts could be caused by unexpected job restarts, instance failures, etc\. This gives the application the same semantics as failure\-free execution during these restarts\. 

If snapshots are enabled for the application, and configured using the application's [ApplicationRestoreConfiguration](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_ApplicationRestoreConfiguration.html), then the service provides exactly\-once processing semantics during application updates, or during service\-related scaling or maintenance\.

## Configuring Checkpointing in Kinesis Data Analytics for Apache Flink<a name="how-fault-configure"></a>

You can configure your application's checkpointing behavior\. You can define whether it persists the checkpointing state, how often it saves its state to checkpoints, and the minimum interval between the end of one checkpoint operation and the beginning of another\.

You configure the following settings using the [https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_CreateApplication.html](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_CreateApplication.html) or [https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_UpdateApplication.html](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_UpdateApplication.html) API operations:
+ `CheckpointingEnabled` — Indicates whether checkpointing is enabled in the application\.
+ `CheckpointInterval` — Contains the time in milliseconds between checkpoint \(persistence\) operations\.
+ `ConfigurationType` — Set this value to `DEFAULT` to use the default checkpointing behavior\. Set this value to `CUSTOM` to configure other values\.
**Note**  
The default checkpoint behavior is as follows:  
**CheckpointingEnabled:** true
**CheckpointInterval:** 60000
**MinPauseBetweenCheckpoints:** 5000
If **ConfigurationType** is set to `DEFAULT`, the preceding values will be used, even if they are set to other values using either using the AWS Command Line Interface, or by setting the values in the application code\.
**Note**  
For Flink 1\.15 onward, Kinesis Data Analytics for Apache Flink will use `stop-with-savepoint` during Automatic Snapshot Creation, that is, application update, scaling or stopping\. 
+ `MinPauseBetweenCheckpoints` — The minimum time in milliseconds between the end of one checkpoint operation and the start of another\. Setting this value prevents the application from checkpointing continuously when a checkpoint operation takes longer than the `CheckpointInterval`\.

## Checkpointing API Examples<a name="how-fault-examples"></a>

This section includes example requests for API actions for configuring checkpointing for an application\. For information about how to use a JSON file for input for an API action, see [Kinesis Data Analytics API Example Code](api-examples.md)\.

### Configure Checkpointing for a New Application<a name="how-fault-examples-create-config"></a>

The following example request for the [https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_CreateApplication.html](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_CreateApplication.html) action configures checkpointing when you are creating an application:

```
{
   "ApplicationName": "MyApplication",
   "RuntimeEnvironment":"FLINK-1_15",
   "ServiceExecutionRole":"arn:aws:iam::123456789123:role/myrole",
   "ApplicationConfiguration": { 
      "ApplicationCodeConfiguration":{
      "CodeContent":{
        "S3ContentLocation":{
          "BucketARN":"arn:aws:s3:::mybucket",
          "FileKey":"myflink.jar",
          "ObjectVersion":"AbCdEfGhIjKlMnOpQrStUvWxYz12345"
        }
      },
      "FlinkApplicationConfiguration": { 
         "CheckpointConfiguration": { 
            "CheckpointingEnabled": "true",
            "CheckpointInterval": 20000,
            "ConfigurationType": "CUSTOM",
            "MinPauseBetweenCheckpoints": 10000
         }
      }
}
```

### Disable Checkpointing for a New Application<a name="how-fault-examples-create-disable"></a>

The following example request for the [https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_CreateApplication.html](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_CreateApplication.html) action disables checkpointing when you are creating an application:

```
{
   "ApplicationName": "MyApplication",
   "RuntimeEnvironment":"FLINK-1_15",
   "ServiceExecutionRole":"arn:aws:iam::123456789123:role/myrole",
   "ApplicationConfiguration": { 
      "ApplicationCodeConfiguration":{
      "CodeContent":{
        "S3ContentLocation":{
          "BucketARN":"arn:aws:s3:::mybucket",
          "FileKey":"myflink.jar",
          "ObjectVersion":"AbCdEfGhIjKlMnOpQrStUvWxYz12345"
        }
      },
      "FlinkApplicationConfiguration": { 
         "CheckpointConfiguration": { 
            "CheckpointingEnabled": "false"
         }
      }
}
```

### Configure Checkpointing for an Existing Application<a name="how-fault-examples-update-config"></a>

The following example request for the [https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_UpdateApplication.html](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_UpdateApplication.html) action configures checkpointing for an existing application:

```
{
   "ApplicationName": "MyApplication",
   "ApplicationConfigurationUpdate": { 
      "FlinkApplicationConfigurationUpdate": { 
         "CheckpointConfigurationUpdate": { 
            "CheckpointingEnabledUpdate": true,
            "CheckpointIntervalUpdate": 20000,
            "ConfigurationTypeUpdate": "CUSTOM",
            "MinPauseBetweenCheckpointsUpdate": 10000
         }
      }
   }
}
```

### Disable Checkpointing for an Existing Application<a name="how-fault-examples-update-update-disable"></a>

The following example request for the [https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_UpdateApplication.html](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_UpdateApplication.html) action disables checkpointing for an existing application:

```
{
   "ApplicationName": "MyApplication",
   "ApplicationConfigurationUpdate": { 
      "FlinkApplicationConfigurationUpdate": { 
         "CheckpointConfigurationUpdate": { 
            "CheckpointingEnabledUpdate": false,
            "CheckpointIntervalUpdate": 20000,
            "ConfigurationTypeUpdate": "CUSTOM",
            "MinPauseBetweenCheckpointsUpdate": 10000
         }
      }
   }
}
```