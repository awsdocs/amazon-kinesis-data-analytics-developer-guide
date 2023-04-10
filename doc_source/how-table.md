# Table API<a name="how-table"></a>

Your Apache Flink application uses the [Apache Flink Table API](https://nightlies.apache.org/flink/flink-docs-release-1.15/dev/table) to interact with data in a stream using a relational model\. You use the Table API to access data using Table sources, and then use Table functions to transform and filter table data\. You can transform and filter tabular data using either API functions or SQL commands\. 

This section contains the following topics:
+ [Table API Connectors](how-table-connectors.md): These components move data between your application and external data sources and destinations\.
+ [Table API Time Attributes](how-table-timeattributes.md): This topic describes how Kinesis Data Analytics tracks events when using the Table API\.