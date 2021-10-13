# Kinesis Data Analytics for Apache Flink Maintenance<a name="maintenance"></a>

Kinesis Data Analytics patches your applications periodically with operating\-system and container\-image security updates to maintain compliance and meet AWS security goals\. The following table lists the default time window during which Kinesis Data Analytics performs this type of maintenance\. Maintenance for your application might happen at any time during the time window that corresponds to your Region\. Your application might experience a downtime of 10 to 30 seconds during this maintenance process\. However, the actual downtime duration depends on the application state\. For information on how to minimize the impact of this downtime, see [Fault tolerance: checkpoints and savepoints](best-practices.md#how-dev-bp-checkpoint)\.

To change the time window during which Kinesis Data Analytics performs maintenance on your application, use the [UpdateApplicationMaintenanceConfiguration](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_UpdateApplicationMaintenanceConfiguration.html) API\.


****  

| Region | Maintenance time window | 
| --- | --- | 
| US East \(N\. Virginia\) | 03:00–11:00 UTC | 
| AWS GovCloud \(US\-East\) | 03:00–11:00 UTC | 
| US East \(Ohio\) | 03:00–11:00 UTC | 
| US West \(N\. California\) | 06:00–14:00 UTC | 
| US West \(Oregon\) | 06:00–14:00 UTC | 
| AWS GovCloud \(US\-West\) | 06:00–14:00 UTC | 
| Asia Pacific \(Hong Kong\) | 13:00–21:00 UTC | 
| Asia Pacific \(Mumbai\) | 16:30–00:30 UTC | 
| Asia Pacific \(Seoul\) | 13:00–21:00 UTC | 
| Asia Pacific \(Singapore\) | 14:00–22:00 UTC | 
| Asia Pacific \(Sydney\) | 12:00–20:00 UTC | 
| Asia Pacific \(Tokyo\) | 13:00–21:00 UTC | 
| Canada \(Central\) | 03:00–11:00 UTC | 
| China \(Beijing\) | 13:00–21:00 UTC | 
| China \(Ningxia\) | 13:00–21:00 UTC | 
| Europe \(Frankfurt\) | 06:00–14:00 UTC | 
| Europe \(Ireland\) | 22:00–06:00 UTC | 
| Europe \(London\) | 22:00–06:00 UTC | 
| Europe \(Milan\) | 21:00–05:00 UTC | 
| Europe \(Paris\) | 23:00–07:00 UTC | 
| Europe \(Stockholm\) | 23:00–07:00 UTC | 
| Middle East \(Bahrain\) | 13:00–21:00 UTC | 
| South America \(São Paulo\) | 19:00–03:00 UTC | 