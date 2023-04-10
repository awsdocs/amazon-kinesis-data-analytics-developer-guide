# Transforming Data Using Operators in Kinesis Data Analytics for Apache Flink With the DataStream API<a name="how-operators"></a>

To transform incoming data in an Amazon Kinesis Data Analytics for Apache Flink, you use an Apache Flink *operator*\. An Apache Flink operator transforms one or more data streams into a new data stream\. The new data stream contains modified data from the original data stream\. Apache Flink provides more than 25 pre\-built stream processing operators\. For more information, see [Operators](https://nightlies.apache.org/flink/flink-docs-release-1.15/dev/stream/operators/) in the [Apache Flink Documentation](https://nightlies.apache.org/flink/flink-docs-release-1.15/)\.

**Topics**
+ [Transform Operators](#how-operators-transform)
+ [Aggregation Operators](#how-operators-agg)

## Transform Operators<a name="how-operators-transform"></a>

The following is an example of a simple text transformation on one of the fields of a JSON data stream\. 

This code creates a transformed data stream\. The new data stream has the same data as the original stream, with the string "` Company`" appended to the contents of the `TICKER` field\.

```
DataStream<ObjectNode> output = input.map(
    new MapFunction<ObjectNode, ObjectNode>() {
        @Override
        public ObjectNode map(ObjectNode value) throws Exception {
            return value.put("TICKER", value.get("TICKER").asText() + " Company");
        }
    }
);
```

## Aggregation Operators<a name="how-operators-agg"></a>

The following is an example of an aggregation operator\. The code creates an aggregated data stream\. The operator creates a 5\-second tumbling window and returns the sum of the `PRICE` values for the records in the window with the same `TICKER` value\.

```
DataStream<ObjectNode> output = input.keyBy(node -> node.get("TICKER").asText())
    .window(TumblingProcessingTimeWindows.of(Time.seconds(5)))
    .reduce((node1, node2) -> {
        double priceTotal = node1.get("PRICE").asDouble() + node2.get("PRICE").asDouble();
        node1.replace("PRICE", JsonNodeFactory.instance.numberNode(priceTotal));
    return node1;
});
```

For a complete code example that uses operators, see [Getting Started \(DataStream API\)](getting-started.md)\. Source code for the Getting Started application is available at [Getting Started](https://github.com/aws-samples/amazon-kinesis-data-analytics-java-examples/tree/master/GettingStarted) in the [Kinesis Data Analytics Java Examples](https://github.com/aws-samples/amazon-kinesis-data-analytics-java-examples) GitHub repository\.