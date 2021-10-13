# Managing Application Backups Using Snapshots<a name="how-fault-snapshot"></a>

A *snapshot* is the Kinesis Data Analytics implementation of an Apache Flink *Savepoint*\. A snapshot is a user\- or service\-triggered, created, and managed backup of the application state\. For information about Apache Flink Savepoints, see [Savepoints](https://ci.apache.org/projects/flink/flink-docs-release-1.11/ops/state/savepoints.html) in the [Apache Flink Documentation](https://ci.apache.org/projects/flink/flink-docs-release-1.11/)\. Using snapshots, you can restart an application from a particular snapshot of application state\.

**Note**  
We recommend that your application create a snapshot several times a day to restart properly with correct state data\. The correct frequency for your snapshots depends on your application's business logic\. Taking frequent snapshots allows you to recover more recent data, but increases cost and requires more system resources\.

In Kinesis Data Analytics, you manage snapshots using the following API actions:
+ [https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_CreateApplicationSnapshot.html](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_CreateApplicationSnapshot.html)
+ [https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_DeleteApplicationSnapshot.html](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_DeleteApplicationSnapshot.html)
+ [https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_DescribeApplicationSnapshot.html](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_DescribeApplicationSnapshot.html)
+ [https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_ListApplicationSnapshots.html](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_ListApplicationSnapshots.html)

For the per\-application limit on the number of snapshots, see [Quota](limits.md)\. If your application reaches the limit on snapshots, then manually creating a snapshot fails with a `LimitExceededException`\. 

Kinesis Data Analytics never deletes snapshots\. You must manually delete your snapshots using the [https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_DeleteApplicationSnapshot.html](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_DeleteApplicationSnapshot.html) action\.

To load a saved snapshot of application state when starting an application, use the [https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_ApplicationRestoreConfiguration.html](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_ApplicationRestoreConfiguration.html) parameter of the [https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_StartApplication.html](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_StartApplication.html) or [https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_UpdateApplication.html](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_UpdateApplication.html) action\.

**Topics**
+ [Automatic Snapshot Creation](#how-fault-snapshot-update)
+ [Restoring From a Snapshot That Contains Incompatible State Data](#how-fault-snapshot-restore)
+ [Snapshot API Examples](#how-fault-snapshot-examples)

## Automatic Snapshot Creation<a name="how-fault-snapshot-update"></a>

If `SnapshotsEnabled` is set to `true` in the [ ApplicationSnapshotConfiguration](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_ApplicationSnapshotConfiguration.html) for the application, Kinesis Data Analytics automatically creates and uses snapshots when the application is updated, scaled, or stopped to provide exactly\-once processing semantics\.

**Note**  
Setting `ApplicationSnapshotConfiguration::SnapshotsEnabled` to `false` will lead to data loss during application updates\.

Automatically created snapshots have the following qualities:
+ The snapshot is managed by the service, but you can see the snapshot using the [ ListApplicationSnapshots](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_ListApplicationSnapshots.html) action\. Automatically created snapshots count against your snapshot limit\.
+ If your application exceeds the snapshot limit, manually created snapshots will fail, but the Kinesis Data Analytics service will still successfully create snapshots when the application is updated, scaled, or stopped\. You must manually delete snapshots using the [ DeleteApplicationSnapshot](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_DeleteApplicationSnapshot.html) action before creating more snapshots manually\.

## Restoring From a Snapshot That Contains Incompatible State Data<a name="how-fault-snapshot-restore"></a>

Because snapshots contain information about operators, restoring state data from a snapshot for an operator that has changed since the previous application version may have unexpected results\. An application will fault if it attempts to restore state data from a snapshot that does not correspond to the current operator\. The faulted application will be stuck in either the `STOPPING` or `UPDATING` state\. 

To allow an application to restore from a snapshot that contains incompatible state data, set the `AllowNonRestoredState` parameter of the [FlinkRunConfiguration](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_FlinkRunConfiguration.html) to `true` using the [UpdateApplication](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_UpdateApplication.html) action\.

You will see the following behavior when an application is restored from an obsolete snapshot:
+ **Operator added:** If a new operator is added, the savepoint has no state data for the new operator\. No fault will occur, and it is not necessary to set `AllowNonRestoredState`\.
+ **Operator deleted:** If an existing operator is deleted, the savepoint has state data for the missing operator\. A fault will occur unless `AllowNonRestoredState` is set to `true`\.
+ **Operator modified:** If compatible changes are made, such as changing a parameter's type to a compatible type, the application can restore from the obsolete snapshot\. For more information about restoring from snapshots, see [Savepoints](https://ci.apache.org/projects/flink/flink-docs-release-1.11/ops/state/savepoints.html) in the *Apache Flink Documentation*\. An application that uses Apache Flink version 1\.8 or later can possibly be restored from a snapshot with a different schema\. An application that uses Apache Flink version 1\.6 cannot be restored\.

If you need to resume an application that is incompatible with existing savepoint data, we recommend that you skip restoring from the snapshot by setting the `ApplicationRestoreType` parameter of the [StartApplication](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_StartApplication.html) action to `SKIP_RESTORE_FROM_SNAPSHOT`\.

For more information about how Apache Flink deals with incompatible state data, see [State Schema Evolution](https://ci.apache.org/projects/flink/flink-docs-release-1.11/dev/stream/state/schema_evolution.html) in the *Apache Flink Documentation*\.

## Snapshot API Examples<a name="how-fault-snapshot-examples"></a>

This section includes example requests for API actions for using snapshots with an application\. For information about how to use a JSON file for input for an API action, see [Kinesis Data Analytics API Example Code](api-examples.md)\.

### Enable Snapshots for an Application<a name="how-fault-savepoint-examples-enable"></a>

The following example request for the [https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_UpdateApplication.html](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_UpdateApplication.html) action enables snapshots for an application:

```
{
   "ApplicationName": "MyApplication",
   "CurrentApplicationVersionId": 1,
   "ApplicationConfigurationUpdate": { 
      "ApplicationSnapshotConfigurationUpdate": { 
         "SnapshotsEnabledUpdate": "true"
       }
    }
}
```

### Create a Snapshot<a name="how-fault-savepoint-examples-create"></a>

The following example request for the [https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_CreateApplicationSnapshot.html](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_CreateApplicationSnapshot.html) action creates a snapshot of the current application state:

```
{
   "ApplicationName": "MyApplication",
   "SnapshotName": "MyCustomSnapshot"
}
```

### List Snapshots for an Application<a name="how-fault-snapshot-examples-list"></a>

The following example request for the [https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_ListApplicationSnapshots.html](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_ListApplicationSnapshots.html) action lists the first 50 snapshots for the current application state:

```
{
   "ApplicationName": "MyApplication",
   "Limit": 50
}
```

### List Details for an Application Snapshot<a name="how-fault-snapshot-examples-describe"></a>

The following example request for the [https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_DescribeApplicationSnapshot.html](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_DescribeApplicationSnapshot.html) action lists details for a specific application snapshot:

```
{
   "ApplicationName": "MyApplication",
   "SnapshotName": "MyCustomSnapshot"
}
```

### Delete a Snapshot<a name="how-fault-snapshot-examples-delete"></a>

The following example request for the [https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_DeleteApplicationSnapshot.html](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_DeleteApplicationSnapshot.html) action deletes a previously saved snapshot\. You can get the `SnapshotCreationTimestamp` value using either [https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_ListApplicationSnapshots.html](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_ListApplicationSnapshots.html) or [https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_DeleteApplicationSnapshot.html](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_DeleteApplicationSnapshot.html):

```
{
   "ApplicationName": "MyApplication",
   "SnapshotName": "MyCustomSnapshot",
   "SnapshotCreationTimestamp": 12345678901.0,
}
```

### Restart an Application Using a Named Snapshot<a name="how-fault-snapshot-examples-load-custom"></a>

The following example request for the [https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_StartApplication.html](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_StartApplication.html) action starts the application using the saved state from a specific snapshot:

```
{
   "ApplicationName": "MyApplication",
   "RunConfiguration": { 
      "ApplicationRestoreConfiguration": { 
         "ApplicationRestoreType": "RESTORE_FROM_CUSTOM_SNAPSHOT",
         "SnapshotName": "MyCustomSnapshot"
      }
   }
}
```

### Restart an Application Using the Most Recent Snapshot<a name="how-fault-snapshot-examples-load-recent"></a>

The following example request for the [https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_StartApplication.html](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_StartApplication.html) action starts the application using the most recent snapshot:

```
{
   "ApplicationName": "MyApplication",
   "RunConfiguration": { 
      "ApplicationRestoreConfiguration": { 
         "ApplicationRestoreType": "RESTORE_FROM_LATEST_SNAPSHOT"
      }
   }
}
```

### Restart an Application Using No Snapshot<a name="how-fault-snapshot-examples-load-none"></a>

The following example request for the [https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_StartApplication.html](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_StartApplication.html) action starts the application without loading application state, even if a snapshot is present:

```
{
   "ApplicationName": "MyApplication",
   "RunConfiguration": { 
      "ApplicationRestoreConfiguration": { 
         "ApplicationRestoreType": "SKIP_RESTORE_FROM_SNAPSHOT"
      }
   }
}
```