# Kinesis Data Analytics for Apache Flink Maintenance<a name="maintenance"></a>

Kinesis Data Analytics patches your applications periodically with operating\-system and container\-image security updates to maintain compliance and meet AWS security goals\. The following table lists the default time window during which Kinesis Data Analytics performs this type of maintenance\. Maintenance for your application might happen at any time during the time window that corresponds to your Region\. Your application might experience a downtime of 10 to 30 seconds during this maintenance process\. However, the actual downtime duration depends on the application state\. For information on how to minimize the impact of this downtime, see [Fault tolerance: checkpoints and savepoints](best-practices.md#how-dev-bp-checkpoint)\.

To change the time window during which Kinesis Data Analytics performs maintenance on your application, use the [UpdateApplicationMaintenanceConfiguration](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_UpdateApplicationMaintenanceConfiguration.html) API\.


****  

| Region | Maintenance time window | 
| --- | --- | 
| AWS GovCloud \(US\-West\) | 06:00–14:00 UTC | 
| AWS GovCloud \(US\-East\) | 03:00–11:00 UTC | 
| US East \(N\. Virginia\) | 03:00–11:00 UTC | 
| US East \(Ohio\) | 03:00–11:00 UTC | 
| US West \(N\. California\) | 06:00–14:00 UTC | 
| US West \(Oregon\) | 06:00–14:00 UTC | 
| Asia Pacific \(Hong Kong\) | 13:00–21:00 UTC | 
| Asia Pacific \(Mumbai\) | 16:30–00:30 UTC | 
| Asia Pacific \(Hyderabad\) | 06:30–14:30 UTC | 
| Asia Pacific \(Seoul\) | 13:00–21:00 UTC | 
| Asia Pacific \(Singapore\) | 14:00–22:00 UTC | 
| Asia Pacific \(Sydney\) | 12:00–20:00 UTC | 
| Asia Pacific \(Jakarta\) | 12:00–20:00 UTC | 
| Asia Pacific \(Tokyo\) | 13:00–21:00 UTC | 
| Canada \(Central\) | 03:00–11:00 UTC | 
| China \(Beijing\) | 13:00–21:00 UTC | 
| China \(Ningxia\) | 13:00–21:00 UTC | 
| Europe \(Frankfurt\) | 06:00–14:00 UTC | 
| Europe \(Zurich\) | 02:00–10:00 UTC | 
| Europe \(Ireland\) | 22:00–06:00 UTC | 
| Europe \(London\) | 22:00–06:00 UTC | 
| Europe \(Stockholm\) | 21:00–05:00 UTC | 
| Europe \(Milan\) | 21:00–05:00 UTC | 
| Europe \(Spain\) | 02:00–10:00 UTC | 
| Africa \(Cape Town\) | 13:00–21:00 UTC | 
| Europe \(Ireland\) | 22:00–06:00 UTC | 
| Europe \(London\) | 23:00–07:00 UTC | 
| Europe \(Paris\) | 23:00–07:00 UTC | 
| Europe \(Stockholm\) | 23:00–07:00 UTC | 
| Middle East \(Bahrain\) | 13:00–21:00 UTC | 
| South America \(São Paulo\) | 19:00–03:00 UTC | 
| Middle East \(UAE\) | 13:00–21:00 UTC | 

## Set a UUID for all operators<a name="maintenance-setting-operator-ids"></a>

When Kinesis Data Analytics starts a Flink job for an application with a snapshot, the Flink job can fail to start due to certain issues\. One of them is *operator ID mismatch*\. Flink expects explicit, consistent operator IDs for Flink job graph operators\. If not set explicitly, Flink auto\-generates an ID for the operators\. This is because Flink uses these operator IDs to uniquely identify the operators in a job graph and uses them to store the state of each operator in a savepoint\.

The *operator ID mismatch* issue happens when Flink does not find a 1:1 mapping between the operator IDs of a job graph and the operator IDs defined in a savepoint\. This happens when explicit consistent operator IDs are not set and Flink auto\-generates operator IDs that may not be consistent with every job graph creation\. The likelihood of applications running into this issue is high during maintenance runs\. To avoid this, we recommend customers set UUID for all operators in flink code\. For more information, see the topic *Set a UUID for all operators* under [Production readiness](https://docs.aws.amazon.com/kinesisanalytics/latest/java/production-readiness.html)\.