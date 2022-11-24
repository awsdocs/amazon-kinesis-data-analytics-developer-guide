# Snapshot manager<a name="snapshot-manager"></a>

 It's a best practice for Flink Applications to regularly trigger savepoints/snapshots to allow for more seamless failure recovery\. Snapshot manager automates this task and offers the following benefits:
+ takes a new snapshot of a running Kinesis Data Analytics for Apache Flink Application
+ gets a count of application snapshots
+ checks if the count is more than the required number of snapshots
+ deletes older snapshots that are older than the required number

For an example, see [Snapshot manager for Flink](https://github.com/aws-samples/amazon-kinesis-data-analytics-snapshot-manager-for-flink)