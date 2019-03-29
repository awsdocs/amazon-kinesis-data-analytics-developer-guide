# Parallelizing Input Streams for Increased Throughput<a name="input-parallelism"></a>

Amazon Kinesis Data Analytics applications can support multiple in\-application input streams, to scale an application beyond the throughput of a single in\-application input stream\. For more information on in\-application input streams, see [Amazon Kinesis Data Analytics for SQL Applications: How It Works](how-it-works.md)\.

In almost all cases, Amazon Kinesis Data Analytics scales your application to handle the capacity of the Kinesis streams or Kinesis Data Firehose source streams that feed into your application\. However, if your source stream's throughput exceeds the throughput of a single in\-application input stream, you can explicitly increase the number of in\-application input streams that your application uses\. You do so with the `InputParallelism` parameter\.

When the `InputParallelism` parameter is greater than one, Amazon Kinesis Data Analytics evenly splits the partitions of your source stream among the in\-application streams\. For instance, if your source stream has 50 shards, and you set `InputParallelism` to `2`, each in\-application input stream receives the input from 25 source stream shards\. 

When you increase the number of in\-application streams, your application must access the data in each stream explicitly\. For information about accessing multiple in\-application streams in your code, see [Accessing Separate In\-Application Streams in Your Amazon Kinesis Data Analytics Application](#input-parallelism-code-example)\.

Although Kinesis Data Streams and Kinesis Data Firehose stream shards are both divided among in\-application streams in the same way, they differ in the way they appear to your application:
+ The records from a Kinesis data stream include a `shard_id` field that can be used to identify the source shard for the record\.
+ The records from a Kinesis Data Firehose delivery stream don't include a field that identifies the record's source shard or partition\. This is because Kinesis Data Firehose abstracts this information away from your application\.

## Evaluating Whether to Increase Your Number of In\-Application Input Streams<a name="input-parallelism-evaluating"></a>

In most cases, a single in\-application input stream can handle the throughput of a single source stream, depending on the complexity and data size of the input streams\. To determine if you need to increase the number of in\-application input streams, you can monitor the `InputBytes` and `MillisBehindLatest` metrics in Amazon CloudWatch\. 

If the `InputBytes` metric is greater that 100 MB/sec \(or you anticipate that it will be greater than this rate\), this can cause an increase in `MillisBehindLatest` and increase the impact of application issues\. To address this, we recommend making the following language choices for your application:
+ Use multiple streams and Kinesis Data Analytics for SQL applications if your application has scaling needs beyond 100 MB/second\.
+ Use [Kinesis Data Analytics for Java Applications](/kinesisanalytics/latest/java/what-is.html) if you want to use a single stream and application\.

If the `MillisBehindLatest` metric has either of the following characteristics, you should increase your application's `InputParallelism` setting:
+ The `MillisBehindLatest` metric is gradually increasing, indicating that your application is falling behind the latest data in the stream\.
+ The `MillisBehindLatest` metric is consistently above 1000 \(one second\)\.

You don't need to increase your application's `InputParallelism` setting if the following are true:
+ The `MillisBehindLatest` metric is gradually decreasing, indicating that your application is catching up to the latest data in the stream\.
+ The `MillisBehindLatest` metric is below 1000 \(one second\)\.

For more information on using CloudWatch, see the [CloudWatch User Guide](http://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/)\.

## Implementing Multiple In\-Application Input Streams<a name="input-parallelism-implementing"></a>

You can set the number of in\-application input streams when an application is created using [CreateApplication](API_CreateApplication.md)\. You set this number after an application is created using [UpdateApplication](API_UpdateApplication.md)\. 

**Note**  
You can only set the `InputParallelism` setting using the Amazon Kinesis Data Analytics API or the AWS CLI\. You cannot set this setting using the AWS Management Console\. For information on setting up the AWS CLI, see [Step 2: Set Up the AWS Command Line Interface \(AWS CLI\)](setup-awscli.md)\.

### Setting a New Application's Input Stream Count<a name="input-parallelism-implementing-create"></a>

The following example demonstrates how to use the `CreateApplication` API action to set a new application's input stream count to 2\. 

For more information about `CreateApplication`, see [CreateApplication](API_CreateApplication.md)\.

```
{
   "ApplicationCode": "<The SQL code the new application will run on the input stream>",
   "ApplicationDescription": "<A friendly description for the new application>",
   "ApplicationName": "<The name for the new application>",
   "Inputs": [ 
    { 
      "InputId": "ID for the new input stream",
      "InputParallelism": { 
        "Count": 2
    }],
   "Outputs": [ ... ],
	}]
}
```

### Setting an Existing Application's Input Stream Count<a name="input-parallelism-implementing-update"></a>

The following example demonstrates how to use the `UpdateApplication` API action to set an existing application's input stream count to 2\.

For more information about `Update_Application`, see [UpdateApplication](API_UpdateApplication.md)\.

```
{
   "InputUpdates": [ 
      { 
         "InputId": "yourInputId",
         "InputParallelismUpdate": { 
            "CountUpdate": 2
         }
      }
   ],
}
```

## Accessing Separate In\-Application Streams in Your Amazon Kinesis Data Analytics Application<a name="input-parallelism-code-example"></a>

To use multiple in\-application input streams in your application, you must explicitly select from the different streams\. The following code example demonstrates how to query multiple input streams in the application created in the Getting Started tutorial\. 

In the following example, each source stream is first aggregated using [COUNT](https://docs.aws.amazon.com/kinesisanalytics/latest/sqlref/sql-reference-count.html) before being combined into a single in\-application stream called `in_application_stream001`\. Aggregating the source streams beforehand helps make sure that the combined in\-application stream can handle the traffic from multiple streams without being overloaded\. 

**Note**  
To run this example and get results from both in\-application input streams, update both the number of shards in your source stream and the `InputParallelism` parameter in your application\.

```
CREATE OR REPLACE STREAM in_application_stream_001 (
    ticker VARCHAR(64),
    ticker_count INTEGER
);

CREATE OR REPLACE PUMP pump001 AS 
INSERT INTO in_application_stream_001
SELECT STREAM ticker_symbol, COUNT(ticker_symbol)
FROM source_sql_stream_001
GROUP BY STEP(source_sql_stream_001.rowtime BY INTERVAL '60' SECOND),
    ticker_symbol; 
        
CREATE OR REPLACE PUMP pump002 AS 
INSERT INTO in_application_stream_001
SELECT STREAM ticker_symbol, COUNT(ticker_symbol)
FROM source_sql_stream_002
GROUP BY STEP(source_sql_stream_002.rowtime BY INTERVAL '60' SECOND),
    ticker_symbol;
```

The preceding code example produces output in `in_application_stream001` similar to the following:

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/kinesisanalytics/latest/dev/images/input-parallelism-results.png)

## Additional Considerations<a name="input-parallelism-considerations"></a>

When using multiple input streams, be aware of the following:
+ The maximum number of in\-application input streams is 64\.
+ The in\-application input streams are distributed evenly among the shards of the application's input stream\.
+ The performance gains from adding in\-application streams don't scale linearly\. That is, doubling the number of in\-application streams doesn't double throughput\. With a typical row size, each in\-application stream can achieve throughput of about 5,000 to 15,000 rows per second\. By increasing the in\-application stream count to 10, you can achieve a throughput of 20,000 to 30,000 rows per second\. Throughput speed is dependent on the count, data types, and data size of the fields in the input stream\.
+ Some aggregate functions \(such as [AVG](https://docs.aws.amazon.com/kinesisanalytics/latest/sqlref/sql-reference-avg.html)\) can produce unexpected results when applied to input streams partitioned into different shards\. Because you need to run the aggregate operation on individual shards before combining them into an aggregate stream, the results might be weighted toward whichever stream contains more records\.
+ If your application continues to experience poor performance \(reflected by a high `MillisBehindLatest` metric\) after you increase your number of input streams, you might have reached your limit of Kinesis Processing Units \(KPUs\)\. For more information, see [Automatically Scaling Applications to Increase Throughput](how-it-works-autoscaling.md)\.