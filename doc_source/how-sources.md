# Adding Streaming Data Sources to Kinesis Data Analytics for Apache Flink<a name="how-sources"></a>

Apache Flink provides connectors for reading from files, sockets, collections, and custom sources\. In your application code, you use an [Apache Flink source](https://ci.apache.org/projects/flink/flink-docs-release-1.11/dev/datastream_api.html#data-sources) to receive data from a stream\. This section describes the sources that are available for Amazon services\.

## Kinesis Data Streams<a name="input-streams"></a>

The `FlinkKinesisConsumer` source provides streaming data to your application from an Amazon Kinesis data stream\. 

### Creating a `FlinkKinesisConsumer`<a name="input-streams-create"></a>

The following code example demonstrates creating a `FlinkKinesisConsumer`:

```
Properties inputProperties = new Properties();
inputProperties.setProperty(ConsumerConfigConstants.AWS_REGION, region);
inputProperties.setProperty(ConsumerConfigConstants.STREAM_INITIAL_POSITION, "LATEST");

DataStream<string> input = env.addSource(new FlinkKinesisConsumer<>(inputStreamName, new SimpleStringSchema(), inputProperties));
```

For more information about using a `FlinkKinesisConsumer`, see [Download and Examine the Apache Flink Streaming Java Code](get-started-exercise.md#get-started-exercise-5)\.

### Creating a `FlinkKinesisConsumer` that uses an EFO consumer<a name="input-streams-efo"></a>

The FlinkKinesisConsumer now supports [Enhanced Fan\-Out \(EFO\)](https://ci.apache.org/projects/flink/flink-docs-release-1.13/docs/connectors/datastream/kinesis/)\. 

If a Kinesis consumer uses EFO, the Kinesis Data Streams service gives it its own dedicated bandwidth, rather than having the consumer share the fixed bandwidth of the stream with the other consumers reading from the stream\.

For more information about using EFO with the Kinesis consumer, see [ FLIP\-128: Enhanced Fan Out for AWS Kinesis Consumers](https://cwiki.apache.org/confluence/display/FLINK/FLIP-128%3A+Enhanced+Fan+Out+for+AWS+Kinesis+Consumers)\.

You enable the EFO consumer by setting the following parameters on the Kinesis consumer:
+ **RECORD\_PUBLISHER\_TYPE: ** Set this parameter to **EFO** for your application to use an EFO consumer to access the Kinesis Data Stream data\. 
+ **EFO\_CONSUMER\_NAME: ** Set this parameter to a string value that is unique among the consumers of this stream\. Re\-using a consumer name in the same Kinesis Data Stream will cause the previous consumer using that name to be terminated\. 

To configure a `FlinkKinesisConsumer` to use EFO, add the following parameters to the consumer:

```
consumerConfig.putIfAbsent(RECORD_PUBLISHER_TYPE, "EFO");
consumerConfig.putIfAbsent(EFO_CONSUMER_NAME, "basic-efo-flink-app");
```

For an example of a Kinesis Data Analytics application that uses an EFO consumer, see [EFO Consumer](examples-efo.md)\.

## Amazon MSK<a name="input-msk"></a>

The `KafkaSource` source provides streaming data to your application from an Amazon MSK topic\. 

### Creating a `KafkaSource`<a name="input-msk-create"></a>

The following code example demonstrates creating a `KafkaSource`:

```
KafkaSource<String> source = KafkaSource.<String>builder()
    .setBootstrapServers(brokers)
    .setTopics("input-topic")
    .setGroupId("my-group")
    .setStartingOffsets(OffsetsInitializer.earliest())
    .setValueOnlyDeserializer(new SimpleStringSchema())
    .build();

env.fromSource(source, WatermarkStrategy.noWatermarks(), "Kafka Source");
```

For more information about using a `KafkaSource`, see [MSK Replication](example-msk.md)\.