# Runtime Properties in Kinesis Data Analytics for Apache Flink<a name="how-properties"></a>

You can use *runtime properties* to configure your application without recompiling your application code\. 

**Topics**
+ [Working with Runtime Properties in the Console](#how-properties-console)
+ [Working with Runtime Properties in the CLI](#how-properties-cli)
+ [Accessing Runtime Properties in a Kinesis Data Analytics Application](#how-properties-access)

## Working with Runtime Properties in the Console<a name="how-properties-console"></a>

You can add, update, or remove runtime properties from your Kinesis Data Analytics application using the console\.

**Note**  
You can't add runtime properties when you create an application in the Kinesis Data Analytics console\.

**Update Runtime Properties for a Kinesis Data Analytics application**

1. Open the Kinesis Data Analytics console at [ https://console\.aws\.amazon\.com/kinesisanalytics](https://console.aws.amazon.com/kinesisanalytics)\.

1. Choose your Kinesis Data Analytics application\. Choose **Application details**\.

1. On the page for your application, choose **Configure**\.

1. Expand the **Properties** section\.

1. Use the controls in the **Properties** section to define a property group with key\-value pairs\. Use these controls to add, update, or remove property groups and runtime properties\.

1. Choose **Update**\.

## Working with Runtime Properties in the CLI<a name="how-properties-cli"></a>

You can add, update, or remove runtime properties using the [AWS CLI](https://docs.aws.amazon.com/cli)\. 

This section includes example requests for API actions for configuring runtime properties for an application\. For information about how to use a JSON file for input for an API action, see [Kinesis Data Analytics API Example Code](api-examples.md)\.

**Note**  
Replace the sample account ID \(*`012345678901`*\) in the examples following with your account ID\.

### Adding Runtime Properties when Creating an Application<a name="how-properties-create"></a>

The following example request for the [https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_CreateApplication.html](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_CreateApplication.html) action adds two runtime property groups \(`ProducerConfigProperties` and `ConsumerConfigProperties`\) when you create an application:

```
{
    "ApplicationName": "MyApplication",
    "ApplicationDescription": "my java test app",
    "RuntimeEnvironment": "FLINK-1_15",
    "ServiceExecutionRole": "arn:aws:iam::012345678901:role/KA-stream-rw-role",
    "ApplicationConfiguration": {
        "ApplicationCodeConfiguration": {
            "CodeContent": {
                "S3ContentLocation": {
                    "BucketARN": "arn:aws:s3:::ka-app-code-username",
                    "FileKey": "java-getting-started-1.0.jar"
                }
            },
            "CodeContentType": "ZIPFILE"
        },
        "EnvironmentProperties":  { 
         "PropertyGroups": [ 
            { 
               "PropertyGroupId": "ProducerConfigProperties",
               "PropertyMap" : {
                    "flink.stream.initpos" : "LATEST",
                    "aws.region" : "us-west-2",
                    "AggregationEnabled" : "false"
               }
            },
            { 
               "PropertyGroupId": "ConsumerConfigProperties",
               "PropertyMap" : {
                    "aws.region" : "us-west-2"
               }
            }
         ]
      }
    }
}
```

### Adding and Updating Runtime Properties in an Existing Application<a name="how-properties-update"></a>

The following example request for the [https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_UpdateApplication.html](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_UpdateApplication.html) action adds or updates runtime properties for an existing application:

```
{
  "ApplicationName": "MyApplication",
  "CurrentApplicationVersionId": 2,
  "ApplicationConfigurationUpdate": {
    "EnvironmentPropertyUpdates": {
      "PropertyGroups": [ 
        { 
          "PropertyGroupId": "ProducerConfigProperties",
          "PropertyMap" : {
            "flink.stream.initpos" : "LATEST",
            "aws.region" : "us-west-2",
            "AggregationEnabled" : "false"
          }
        },
        { 
          "PropertyGroupId": "ConsumerConfigProperties",
          "PropertyMap" : {
            "aws.region" : "us-west-2"
          }
        }
      ]
    }
  }
}
```

**Note**  
If you use a key that has no corresponding runtime property in a property group, Kinesis Data Analytics adds the key\-value pair as a new property\. If you use a key for an existing runtime property in a property group, Kinesis Data Analytics updates the property value\. 

### Removing Runtime Properties<a name="how-properties-remove"></a>

The following example request for the [https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_UpdateApplication.html](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_UpdateApplication.html) action removes all runtime properties and property groups from an existing application:

```
{
  "ApplicationName": "MyApplication",
  "CurrentApplicationVersionId": 3,
  "ApplicationConfigurationUpdate": {
    "EnvironmentPropertyUpdates": {
      "PropertyGroups": []
    }
  }
}
```

**Important**  
If you omit an existing property group or an existing property key in a property group, that property group or property is removed\.

## Accessing Runtime Properties in a Kinesis Data Analytics Application<a name="how-properties-access"></a>

You retrieve runtime properties in your Java application code using the static `KinesisAnalyticsRuntime.getApplicationProperties()` method, which returns a `Map<String, Properties>` object\.

The following Java code example retrieves runtime properties for your application:

```
 Map<String, Properties> applicationProperties = KinesisAnalyticsRuntime.getApplicationProperties();
```

You retrieve a property group \(as a `Java.Util.Properties` object\) as follows:

```
Properties consumerProperties = applicationProperties.get("ConsumerConfigProperties");
```

You typically configure an Apache Flink source or sink by passing in the `Properties` object without needing to retrieve the individual properties\. The following code example demonstrates how to create an Flink source by passing in a `Properties` object retrieved from runtime properties:

```
private static FlinkKinesisProducer<String> createSinkFromApplicationProperties() throws IOException {
  Map<String, Properties> applicationProperties = KinesisAnalyticsRuntime.getApplicationProperties();
  FlinkKinesisProducer<String> sink = new FlinkKinesisProducer<String>(new SimpleStringSchema(),
    applicationProperties.get("ProducerConfigProperties"));

  sink.setDefaultStream(outputStreamName);
  sink.setDefaultPartition("0");
  return sink;
}
```

For a complete code example that uses runtime properties, see [Getting Started \(DataStream API\)](getting-started.md)\. Source code for the Getting Started application is available at [Getting Started](https://github.com/aws-samples/amazon-kinesis-data-analytics-java-examples/tree/master/GettingStarted) in the [Kinesis Data Analytics Java Examples](https://github.com/aws-samples/amazon-kinesis-data-analytics-java-examples) GitHub repository\.