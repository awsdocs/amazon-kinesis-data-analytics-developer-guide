# Table API Connectors<a name="how-table-connectors"></a>

In the Apache Flink programming model, connectors are components that your application uses to read or write data from external sources, such as other AWS services\.

With the Apache Flink Table API, you can use the following types of connectors:
+ [Table API Sources](#how-table-connectors-source): You use Table API source connectors to create tables within your `TableEnvironment` using either API calls or SQL queries\.
+ [Table API Sinks](#how-table-connectors-sink): You use SQL commands to write table data to external sources such as an Amazon MSK topic or an Amazon S3 bucket\.

## Table API Sources<a name="how-table-connectors-source"></a>

You create a table source from a data stream\. The following code creates a table from an Amazon MSK topic:

```
//create the table
    final FlinkKafkaConsumer<StockRecord> consumer = new FlinkKafkaConsumer<StockRecord>(kafkaTopic, new KafkaEventDeserializationSchema(), kafkaProperties);
    consumer.setStartFromEarliest();
    //Obtain stream
    DataStream<StockRecord> events = env.addSource(consumer);

    Table table = streamTableEnvironment.fromDataStream(events);
```

For more information about table sources, see [Table & Connectors ](https://ci.apache.org/projects/flink/flink-docs-release-1.11/dev/table/connectors/) in the [Apache Flink documentation](https://ci.apache.org/projects/flink/flink-docs-release-1.11/)\.

## Table API Sinks<a name="how-table-connectors-sink"></a>

To write table data to a sink, you create the sink in SQL, and then run the SQL\-based sink on the `StreamTableEnvironment` object\.

The following code example demonstrates how to write table data to an Amazon S3 sink:

```
final String s3Sink = "CREATE TABLE sink_table (" +
    "event_time TIMESTAMP," +
    "ticker STRING," +
    "price DOUBLE," +
    "dt STRING," +
    "hr STRING" +
    ")" +
    " PARTITIONED BY (ticker,dt,hr)" +
    " WITH" +
    "(" +
    " 'connector' = 'filesystem'," +
    " 'path' = '" + s3Path + "'," +
    " 'format' = 'json'" +
    ") ";

    //send to s3
    streamTableEnvironment.executeSql(s3Sink);
    filteredTable.executeInsert("sink_table");
```

 You can use the `format` parameter to control what format Kinesis Data Analytics uses to write the output to the sink\. For information about formats, see [ Formats](https://ci.apache.org/projects/flink/flink-docs-stable/dev/table/connect.html#formats) in the [Apache Flink documentation](https://ci.apache.org/projects/flink/flink-docs-stable/)\.

For more information about table sinks, see [Table & Connectors ](https://ci.apache.org/projects/flink/flink-docs-release-1.11/dev/table/connectors/) in the [Apache Flink documentation](https://ci.apache.org/projects/flink/flink-docs-release-1.11/)\.

## User\-Defined Sources and Sinks<a name="how-table-connectors-userdef"></a>

You can use existing Apache Kafka connectors for sending data to and from other AWS services, such as Amazon MSK and Amazon S3\. For interacting with other data sources and destinations, you can define your own sources and sinks\. For more information, see [ User\-Defined Sources and Sinks](https://ci.apache.org/projects/flink/flink-docs-release-1.11/dev/table/sourceSinks.html) in the [Apache Flink documentation](https://ci.apache.org/projects/flink/flink-docs-release-1.11/)\.