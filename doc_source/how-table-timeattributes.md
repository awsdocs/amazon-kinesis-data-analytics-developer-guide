# Table API Time Attributes<a name="how-table-timeattributes"></a>

Each record in a data stream has several timestamps that define when events related to the record occurred:
+ **Event Time**: A user\-defined timestamp that defines when the event that created the record occurred\.
+ **Ingestion Time**: The time when your application retrieved the record from the data stream\.
+ **Processing Time**: The time when your application processed the record\.

When the Apache Flink Table API creates windows based on record times, you define which of these timestamps it uses by using the [setStreamTimeCharacteristic](https://ci.apache.org/projects/flink/flink-docs-release-1.11/api/java/org/apache/flink/streaming/api/environment/StreamExecutionEnvironment.html#setStreamTimeCharacteristic-org.apache.flink.streaming.api.TimeCharacteristic-) method\.

For more information about using timestamps with the Table API, see [ Time Attributes](https://ci.apache.org/projects/flink/flink-docs-release-1.11/dev/table/streaming/time_attributes.html) in the [Apache Flink documentation](https://ci.apache.org/projects/flink/flink-docs-release-1.11/)\.