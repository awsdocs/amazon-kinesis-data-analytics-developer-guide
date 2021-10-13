# Writing Data Using Sinks in Kinesis Data Analytics for Apache Flink<a name="how-sinks"></a>

In your application code, you use an [Apache Flink sink](https://ci.apache.org/projects/flink/flink-docs-release-1.11/dev/datastream_api.html#data-sinks) to write data from an Apache Flink stream to an AWS service, such as Kinesis Data Streams\.

Apache Flink provides sinks for files, sockets, and custom sinks\. The following sinks are available for AWS:

## Kinesis Data Streams<a name="sinks-streams"></a>

Apache Flink provides information about the [Kinesis Data Streams Connector](https://ci.apache.org/projects/flink/flink-docs-release-1.11/dev/connectors/kinesis.html) in the Apache Flink documentation\.

For an example of an application that uses a Kinesis data stream for input and output, see [Getting Started \(DataStream API\)](getting-started.md)\.

## Amazon S3<a name="sinks-s3"></a>

You can use the Apache Flink `StreamingFileSink` to write objects to an Amazon S3 bucket\.

For an example about how to write objects to S3, see [Example: Writing to an Amazon S3 Bucket](examples-s3.md)\. 

## Kinesis Data Firehose<a name="sinks-firehose"></a>

The `FlinkKinesisFirehoseProducer` is a reliable, scalable Apache Flink sink for storing application output using the [Kinesis Data Firehose](https://docs.aws.amazon.com/firehose/latest/dev/) service\. This section describes how to set up a Maven project to create and use a `FlinkKinesisFirehoseProducer`\.

**Topics**
+ [Creating a `FlinkKinesisFirehoseProducer`](#sinks-firehose-create)
+ [`FlinkKinesisFirehoseProducer` Code Example](#sinks-firehose-sample)

### Creating a `FlinkKinesisFirehoseProducer`<a name="sinks-firehose-create"></a>

The following code example demonstrates creating a `FlinkKinesisFirehoseProducer`:

```
Properties outputProperties = new Properties();
outputProperties.setProperty(ConsumerConfigConstants.AWS_REGION, region);

FlinkKinesisFirehoseProducer<String> sink = new FlinkKinesisFirehoseProducer<>(outputStreamName, new SimpleStringSchema(), outputProperties);
```

### `FlinkKinesisFirehoseProducer` Code Example<a name="sinks-firehose-sample"></a>

The following code example demonstrates how to create and configure a `FlinkKinesisFirehoseProducer` and send data from an Apache Flink data stream to the Kinesis Data Firehose service\.

```
 
package com.amazonaws.services.kinesisanalytics;

import com.amazonaws.services.kinesisanalytics.flink.connectors.config.ProducerConfigConstants;
import com.amazonaws.services.kinesisanalytics.flink.connectors.producer.FlinkKinesisFirehoseProducer;
import com.amazonaws.services.kinesisanalytics.runtime.KinesisAnalyticsRuntime;
import org.apache.flink.api.common.serialization.SimpleStringSchema;
import org.apache.flink.streaming.api.datastream.DataStream;
import org.apache.flink.streaming.api.environment.StreamExecutionEnvironment;
import org.apache.flink.streaming.connectors.kinesis.FlinkKinesisConsumer;
import org.apache.flink.streaming.connectors.kinesis.FlinkKinesisProducer;

import org.apache.flink.streaming.connectors.kinesis.config.ConsumerConfigConstants;

import java.io.IOException;
import java.util.Map;
import java.util.Properties;



public class StreamingJob {

	private static final String region = "us-east-1";
	private static final String inputStreamName = "ExampleInputStream";
	private static final String outputStreamName = "ExampleOutputStream";

	private static DataStream<String> createSourceFromStaticConfig(StreamExecutionEnvironment env) {
		Properties inputProperties = new Properties();
		inputProperties.setProperty(ConsumerConfigConstants.AWS_REGION, region);
		inputProperties.setProperty(ConsumerConfigConstants.STREAM_INITIAL_POSITION, "LATEST");

		return env.addSource(new FlinkKinesisConsumer<>(inputStreamName, new SimpleStringSchema(), inputProperties));
	}

	private static DataStream<String> createSourceFromApplicationProperties(StreamExecutionEnvironment env) throws IOException {
		Map<String, Properties> applicationProperties = KinesisAnalyticsRuntime.getApplicationProperties();
		return env.addSource(new FlinkKinesisConsumer<>(inputStreamName, new SimpleStringSchema(),
				applicationProperties.get("ConsumerConfigProperties")));
	}

	private static FlinkKinesisFirehoseProducer<String> createFirehoseSinkFromStaticConfig() {
		/*
		 * com.amazonaws.services.kinesisanalytics.flink.connectors.config.ProducerConfigConstants
		 * lists of all of the properties that firehose sink can be configured with.
		 */

		Properties outputProperties = new Properties();
		outputProperties.setProperty(ConsumerConfigConstants.AWS_REGION, region);

		FlinkKinesisFirehoseProducer<String> sink = new FlinkKinesisFirehoseProducer<>(outputStreamName, new SimpleStringSchema(), outputProperties);
		ProducerConfigConstants config = new ProducerConfigConstants();
		return sink;
	}

	private static FlinkKinesisFirehoseProducer<String> createFirehoseSinkFromApplicationProperties() throws IOException {
		/*
		 * com.amazonaws.services.kinesisanalytics.flink.connectors.config.ProducerConfigConstants
		 * lists of all of the properties that firehose sink can be configured with.
		 */

		Map<String, Properties> applicationProperties = KinesisAnalyticsRuntime.getApplicationProperties();
		FlinkKinesisFirehoseProducer<String> sink = new FlinkKinesisFirehoseProducer<>(outputStreamName, new SimpleStringSchema(),
				applicationProperties.get("ProducerConfigProperties"));
		return sink;
	}

	public static void main(String[] args) throws Exception {
		// set up the streaming execution environment
		final StreamExecutionEnvironment env = StreamExecutionEnvironment.getExecutionEnvironment();


         /* if you would like to use runtime configuration properties, uncomment the lines below
         * DataStream<String> input = createSourceFromApplicationProperties(env);
         */

		DataStream<String> input = createSourceFromStaticConfig(env);

		// Kinesis Firehose sink
		input.addSink(createFirehoseSinkFromStaticConfig());

		// If you would like to use runtime configuration properties, uncomment the lines below
		// input.addSink(createFirehoseSinkFromApplicationProperties());

		env.execute("Flink Streaming Java API Skeleton");
	}
}
```

For a complete tutorial about how to use the Kinesis Data Firehose sink, see [Example: Writing to Kinesis Data Firehose](get-started-exercise-fh.md)\.