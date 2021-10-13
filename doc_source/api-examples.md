# Kinesis Data Analytics API Example Code<a name="api-examples"></a>

This topic contains example request blocks for Kinesis Data Analytics actions\.

To use JSON as the input for an action with the AWS Command Line Interface \(AWS CLI\), save the request in a JSON file\. Then pass the file name into the action using the `--cli-input-json` parameter\. 

The following example demonstrates how to use a JSON file with an action\.

```
$ aws kinesisanalyticsv2 start-application --cli-input-json file://start.json
```

For more information about using JSON with the AWS CLI, see [Generate CLI Skeleton and CLI Input JSON Parameters](https://docs.aws.amazon.com/cli/latest/userguide/generate-cli-skeleton.html) in the *AWS Command Line Interface User Guide*\.

**Topics**
+ [AddApplicationCloudWatchLoggingOption](#api-examples-addapplicationcloudwatchloggingoption)
+ [AddApplicationInput](#api-examples-addapplicationinput)
+ [AddApplicationInputProcessingConfiguration](#api-examples-addapplicationinputprocessingconfiguration)
+ [AddApplicationOutput](#api-examples-addapplicationoutput)
+ [AddApplicationReferenceDataSource](#api-examples-addapplicationreferencedatasource)
+ [AddApplicationVpcConfiguration](#api-examples-AddApplicationVpcConfiguration)
+ [CreateApplication](#api-examples-createapplication)
+ [CreateApplicationSnapshot](#api-examples-createapplicationsnapshot)
+ [DeleteApplication](#api-examples-deleteapplication)
+ [DeleteApplicationCloudWatchLoggingOption](#api-examples-deleteapplicationcloudwatchloggingoption)
+ [DeleteApplicationInputProcessingConfiguration](#api-examples-deleteapplicationinputprocessingconfiguration)
+ [DeleteApplicationOutput](#api-examples-deleteapplicationoutput)
+ [DeleteApplicationReferenceDataSource](#api-examples-deleteapplicationreferencedatasource)
+ [DeleteApplicationSnapshot](#api-examples-deleteapplicationsnapshot)
+ [DeleteApplicationVpcConfiguration](#api-examples-DeleteApplicationVpcConfiguration)
+ [DescribeApplication](#api-examples-describeapplication)
+ [DescribeApplicationSnapshot](#api-examples-describeapplicationsnapshot)
+ [DiscoverInputSchema](#api-examples-discoverinputschema)
+ [ListApplications](#api-examples-listapplications)
+ [ListApplicationSnapshots](#api-examples-listapplicationsnapshots)
+ [StartApplication](#api-examples-startapplication)
+ [StopApplication](#api-examples-stopapplication)
+ [UpdateApplication](#api-examples-updateapplication)

## AddApplicationCloudWatchLoggingOption<a name="api-examples-addapplicationcloudwatchloggingoption"></a>

The following example request code for the [AddApplicationCloudWatchLoggingOption](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_AddApplicationCloudWatchLoggingOption.html) action adds an Amazon CloudWatch logging option to a Kinesis Data Analytics application:

```
{
    "ApplicationName": "MyApplication",
    "CloudWatchLoggingOption": {
        "LogStreamARN": "arn:aws:logs:us-east-1:123456789123:log-group:my-log-group:log-stream:My-LogStream"
    },
    "CurrentApplicationVersionId": 2
}
```

## AddApplicationInput<a name="api-examples-addapplicationinput"></a>

The following example request code for the [AddApplicationInput](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_AddApplicationInput.html) action adds an application input to a Kinesis Data Analytics application:

```
{
   "ApplicationName": "MyApplication",
   "CurrentApplicationVersionId": 2,
   "Input": { 
      "InputParallelism": { 
         "Count": 2
      },
      "InputSchema": { 
         "RecordColumns": [ 
            { 
               "Mapping": "$.TICKER",
               "Name": "TICKER_SYMBOL",
               "SqlType": "VARCHAR(50)"
            },
            {
                "SqlType": "REAL",
                "Name": "PRICE",
                "Mapping": "$.PRICE"
            }
         ],
         "RecordEncoding": "UTF-8",
         "RecordFormat": { 
            "MappingParameters": { 
               "JSONMappingParameters": { 
                  "RecordRowPath": "$"
               }
            },
            "RecordFormatType": "JSON"
         }
      },
      "KinesisStreamsInput": { 
         "ResourceARN": "arn:aws:kinesis:us-east-1:012345678901:stream/ExampleInputStream"
      }
   }
}
```

## AddApplicationInputProcessingConfiguration<a name="api-examples-addapplicationinputprocessingconfiguration"></a>

The following example request code for the [AddApplicationInputProcessingConfiguration](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_AddApplicationInputProcessingConfiguration.html) action adds an application input processing configuration to a Kinesis Data Analytics application:

```
{
   "ApplicationName": "MyApplication",
   "CurrentApplicationVersionId": 2,
   "InputId": "2.1",
   "InputProcessingConfiguration": { 
      "InputLambdaProcessor": { 
         "ResourceARN": "arn:aws:lambda:us-east-1:012345678901:function:MyLambdaFunction"
      }
   }
}
```

## AddApplicationOutput<a name="api-examples-addapplicationoutput"></a>

The following example request code for the [AddApplicationOutput](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_AddApplicationOutput.html) action adds a Kinesis data stream as an application output to a Kinesis Data Analytics application:

```
{
   "ApplicationName": "MyApplication",
   "CurrentApplicationVersionId": 2,
   "Output": { 
      "DestinationSchema": { 
         "RecordFormatType": "JSON"
      },
      "KinesisStreamsOutput": { 
         "ResourceARN": "arn:aws:kinesis:us-east-1:012345678901:stream/ExampleOutputStream"
      },
      "Name": "DESTINATION_SQL_STREAM"
   }
}
```

## AddApplicationReferenceDataSource<a name="api-examples-addapplicationreferencedatasource"></a>

The following example request code for the [AddApplicationReferenceDataSource](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_AddApplicationReferenceDataSource.html) action adds a CSV application reference data source to a Kinesis Data Analytics application:

```
{
   "ApplicationName": "MyApplication",
   "CurrentApplicationVersionId": 5,
   "ReferenceDataSource": { 
      "ReferenceSchema": { 
         "RecordColumns": [ 
            { 
               "Mapping": "$.TICKER",
               "Name": "TICKER",
               "SqlType": "VARCHAR(4)"
            },
            { 
               "Mapping": "$.COMPANYNAME",
               "Name": "COMPANY_NAME",
               "SqlType": "VARCHAR(40)"
            },
         ],
         "RecordEncoding": "UTF-8",
         "RecordFormat": { 
            "MappingParameters": { 
               "CSVMappingParameters": { 
                  "RecordColumnDelimiter": " ",
                  "RecordRowDelimiter": "\r\n"
               }
            },
            "RecordFormatType": "CSV"
         }
      },
      "S3ReferenceDataSource": { 
         "BucketARN": "arn:aws:s3:::MyS3Bucket",
         "FileKey": "TickerReference.csv"
      },
      "TableName": "string"
   }
}
```

## AddApplicationVpcConfiguration<a name="api-examples-AddApplicationVpcConfiguration"></a>

The following example request code for the [AddApplicationVpcConfiguration](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_AddApplicationVpcConfiguration.html) action adds a VPC configuration to an existing application:

```
{
   "ApplicationName": "MyApplication",
   "CurrentApplicationVersionId": 9,
   "VpcConfiguration": { 
      "SecurityGroupIds": [ "sg-0123456789abcdef0" ],
      "SubnetIds": [ "subnet-0123456789abcdef0" ]
   }
}
```

## CreateApplication<a name="api-examples-createapplication"></a>

The following example request code for the [CreateApplication](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_CreateApplication.html) action creates a Kinesis Data Analytics application:

```
{
  "ApplicationName":"MyApplication",
  "ApplicationDescription":"My-Application-Description",
  "RuntimeEnvironment":"FLINK-1_13",
  "ServiceExecutionRole":"arn:aws:iam::123456789123:role/myrole",
  "CloudWatchLoggingOptions":[
    {
      "LogStreamARN":"arn:aws:logs:us-east-1:123456789123:log-group:my-log-group:log-stream:My-LogStream"
    }
  ],
  "ApplicationConfiguration": {
    "EnvironmentProperties":
      {"PropertyGroups": 
        [
          {"PropertyGroupId": "ConsumerConfigProperties",
            "PropertyMap": 
              {"aws.region": "us-east-1",
              "flink.stream.initpos": "LATEST"}
          },
          {"PropertyGroupId": "ProducerConfigProperties",
            "PropertyMap": 
              {"aws.region": "us-east-1"}
          },
        ]
      },
    "ApplicationCodeConfiguration":{
      "CodeContent":{
        "S3ContentLocation":{
          "BucketARN":"arn:aws:s3:::mybucket",
          "FileKey":"myflink.jar",
          "ObjectVersion":"AbCdEfGhIjKlMnOpQrStUvWxYz12345"
        }
      },
      "CodeContentType":"ZIPFILE"
    },
      "FlinkApplicationConfiguration":{
      "ParallelismConfiguration":{
        "ConfigurationType":"CUSTOM",
        "Parallelism":2,
        "ParallelismPerKPU":1,
        "AutoScalingEnabled":true
      }
    }
  }
}
```

## CreateApplicationSnapshot<a name="api-examples-createapplicationsnapshot"></a>

The following example request code for the [CreateApplicationSnapshot](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_CreateApplicationSnapshot.html) action creates a snapshot of application state:

```
{
   "ApplicationName": "MyApplication",
   "SnapshotName": "MySnapshot"
}
```

## DeleteApplication<a name="api-examples-deleteapplication"></a>

The following example request code for the [DeleteApplication](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_DeleteApplication.html) action deletes a Kinesis Data Analytics application:

```
{"ApplicationName": "MyApplication",
"CreateTimestamp": 12345678912}
```

## DeleteApplicationCloudWatchLoggingOption<a name="api-examples-deleteapplicationcloudwatchloggingoption"></a>

The following example request code for the [DeleteApplicationCloudWatchLoggingOption](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_DeleteApplicationCloudWatchLoggingOption.html) action deletes an Amazon CloudWatch logging option from a Kinesis Data Analytics application:

```
{
    "ApplicationName": "MyApplication",
    "CloudWatchLoggingOptionId": "3.1"
    "CurrentApplicationVersionId": 3
}
```

## DeleteApplicationInputProcessingConfiguration<a name="api-examples-deleteapplicationinputprocessingconfiguration"></a>

The following example request code for the [DeleteApplicationInputProcessingConfiguration](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_DeleteApplicationInputProcessingConfiguration.html) action removes an input processing configuration from a Kinesis Data Analytics application:

```
{
   "ApplicationName": "MyApplication",
   "CurrentApplicationVersionId": 4,
   "InputId": "2.1"
}
```

## DeleteApplicationOutput<a name="api-examples-deleteapplicationoutput"></a>

The following example request code for the [DeleteApplicationOutput](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_DeleteApplicationOutput.html) action removes an application output from a Kinesis Data Analytics application:

```
{
   "ApplicationName": "MyApplication",
   "CurrentApplicationVersionId": 4,
   "OutputId": "4.1"
}
```

## DeleteApplicationReferenceDataSource<a name="api-examples-deleteapplicationreferencedatasource"></a>

The following example request code for the [DeleteApplicationReferenceDataSource](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_DeleteApplicationReferenceDataSource.html) action removes an application reference data source from a Kinesis Data Analytics application:

```
{
   "ApplicationName": "MyApplication",
   "CurrentApplicationVersionId": 5,
   "ReferenceId": "5.1"
}
```

## DeleteApplicationSnapshot<a name="api-examples-deleteapplicationsnapshot"></a>

The following example request code for the [DeleteApplicationSnapshot](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_DeleteApplicationSnapshot.html) action deletes a snapshot of application state:

```
{
   "ApplicationName": "MyApplication",
   "SnapshotCreationTimestamp": 12345678912,
   "SnapshotName": "MySnapshot"
}
```

## DeleteApplicationVpcConfiguration<a name="api-examples-DeleteApplicationVpcConfiguration"></a>

The following example request code for the [DeleteApplicationVpcConfiguration](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_DeleteApplicationVpcConfiguration.html) action removes an existing VPC configuration from an application:

```
{
   "ApplicationName": "MyApplication",
   "CurrentApplicationVersionId": 9,
   "VpcConfigurationId": "1.1"
}
```

## DescribeApplication<a name="api-examples-describeapplication"></a>

The following example request code for the [DescribeApplication](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_DescribeApplication.html) action returns details about a Kinesis Data Analytics application:

```
{"ApplicationName": "MyApplication"}
```

## DescribeApplicationSnapshot<a name="api-examples-describeapplicationsnapshot"></a>

The following example request code for the [DescribeApplicationSnapshot](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_DescribeApplicationSnapshot.html) action returns details about a snapshot of application state:

```
{
   "ApplicationName": "MyApplication",
   "SnapshotName": "MySnapshot"
}
```

## DiscoverInputSchema<a name="api-examples-discoverinputschema"></a>

The following example request code for the [DiscoverInputSchema](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_DiscoverInputSchema.html) action generates a schema from a streaming source:

```
{
   "InputProcessingConfiguration": { 
      "InputLambdaProcessor": { 
         "ResourceARN": "arn:aws:lambda:us-east-1:012345678901:function:MyLambdaFunction"
      }
   },
   "InputStartingPositionConfiguration": { 
      "InputStartingPosition": "NOW"
   },
   "ResourceARN": "arn:aws:kinesis:us-east-1:012345678901:stream/ExampleInputStream",
   "S3Configuration": { 
      "BucketARN": "string",
      "FileKey": "string"
   },
   "ServiceExecutionRole": "string"
}
```

The following example request code for the [DiscoverInputSchema](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_DiscoverInputSchema.html) action generates a schema from a reference source:

```
{
   "S3Configuration": { 
      "BucketARN": "arn:aws:s3:::mybucket",
      "FileKey": "TickerReference.csv"
   },
   "ServiceExecutionRole": "arn:aws:iam::123456789123:role/myrole"
}
```

## ListApplications<a name="api-examples-listapplications"></a>

The following example request code for the [ListApplications](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_ListApplications.html) action returns a list of Kinesis Data Analytics applications in your account:

```
{
   "ExclusiveStartApplicationName": "MyApplication",
   "Limit": 50
}
```

## ListApplicationSnapshots<a name="api-examples-listapplicationsnapshots"></a>

The following example request code for the [ListApplicationSnapshots](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_ListApplicationSnapshots.html) action returns a list of snapshots of application state:

```
{"ApplicationName": "MyApplication",
   "Limit": 50,
   "NextToken": "aBcDeFgHiJkLmNoPqRsTuVwXyZ0123"
}
```

## StartApplication<a name="api-examples-startapplication"></a>

The following example request code for the [StartApplication](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_StartApplication.html) action starts a Kinesis Data Analytics application, and loads the application state from the latest snapshot \(if any\):

```
{
    "ApplicationName": "MyApplication",
    "RunConfiguration": {
        "ApplicationRestoreConfiguration": { 
         "ApplicationRestoreType": "RESTORE_FROM_LATEST_SNAPSHOT"
         }
    }
}
```

## StopApplication<a name="api-examples-stopapplication"></a>

The following example request code for the [API\_StopApplication](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_StopApplication.html) action stops a Kinesis Data Analytics application:

```
{"ApplicationName": "MyApplication"}
```

## UpdateApplication<a name="api-examples-updateapplication"></a>

The following example request code for the [UpdateApplication](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_UpdateApplication.html) action updates a Kinesis Data Analytics application to change the location of the application code:

```
{"ApplicationName": "MyApplication",
"CurrentApplicationVersionId": 1,
"ApplicationConfigurationUpdate": { 
      "ApplicationCodeConfigurationUpdate": { 
         "CodeContentTypeUpdate": "ZIPFILE",
         "CodeContentUpdate": { 
            "S3ContentLocationUpdate": { 
               "BucketARNUpdate": "arn:aws:s3:::my_new_bucket",
               "FileKeyUpdate": "my_new_code.zip",
               "ObjectVersionUpdate": "2"
         }
      }
   }
}
```