# DescribeApplication<a name="API_DescribeApplication"></a>

**Note**  
This documentation is for version 1 of the Amazon Kinesis Data Analytics API, which only supports SQL applications\. Version 2 of the API supports SQL and Java applications\. For more information about version 2, see [Amazon Kinesis Data Analytics API V2 Documentation](/kinesisanalytics/latest/apiv2/Welcome.html)\.

Returns information about a specific Amazon Kinesis Analytics application\.

If you want to retrieve a list of all applications in your account, use the [ListApplications](https://docs.aws.amazon.com/kinesisanalytics/latest/dev/API_ListApplications.html) operation\.

This operation requires permissions to perform the `kinesisanalytics:DescribeApplication` action\. You can use `DescribeApplication` to get the current application versionId, which you need to call other operations such as `Update`\. 

## Request Syntax<a name="API_DescribeApplication_RequestSyntax"></a>

```
{
   "[ApplicationName](#analytics-DescribeApplication-request-ApplicationName)": "string"
}
```

## Request Parameters<a name="API_DescribeApplication_RequestParameters"></a>

The request accepts the following data in JSON format\.

 ** [ApplicationName](#API_DescribeApplication_RequestSyntax) **   <a name="analytics-DescribeApplication-request-ApplicationName"></a>
Name of the application\.  
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 128\.  
Pattern: `[a-zA-Z0-9_.-]+`   
Required: Yes

## Response Syntax<a name="API_DescribeApplication_ResponseSyntax"></a>

```
{
   "[ApplicationDetail](#analytics-DescribeApplication-response-ApplicationDetail)": { 
      "[ApplicationARN](API_ApplicationDetail.md#analytics-Type-ApplicationDetail-ApplicationARN)": "string",
      "[ApplicationCode](API_ApplicationDetail.md#analytics-Type-ApplicationDetail-ApplicationCode)": "string",
      "[ApplicationDescription](API_ApplicationDetail.md#analytics-Type-ApplicationDetail-ApplicationDescription)": "string",
      "[ApplicationName](API_ApplicationDetail.md#analytics-Type-ApplicationDetail-ApplicationName)": "string",
      "[ApplicationStatus](API_ApplicationDetail.md#analytics-Type-ApplicationDetail-ApplicationStatus)": "string",
      "[ApplicationVersionId](API_ApplicationDetail.md#analytics-Type-ApplicationDetail-ApplicationVersionId)": number,
      "[CloudWatchLoggingOptionDescriptions](API_ApplicationDetail.md#analytics-Type-ApplicationDetail-CloudWatchLoggingOptionDescriptions)": [ 
         { 
            "[CloudWatchLoggingOptionId](API_CloudWatchLoggingOptionDescription.md#analytics-Type-CloudWatchLoggingOptionDescription-CloudWatchLoggingOptionId)": "string",
            "[LogStreamARN](API_CloudWatchLoggingOptionDescription.md#analytics-Type-CloudWatchLoggingOptionDescription-LogStreamARN)": "string",
            "[RoleARN](API_CloudWatchLoggingOptionDescription.md#analytics-Type-CloudWatchLoggingOptionDescription-RoleARN)": "string"
         }
      ],
      "[CreateTimestamp](API_ApplicationDetail.md#analytics-Type-ApplicationDetail-CreateTimestamp)": number,
      "[InputDescriptions](API_ApplicationDetail.md#analytics-Type-ApplicationDetail-InputDescriptions)": [ 
         { 
            "[InAppStreamNames](API_InputDescription.md#analytics-Type-InputDescription-InAppStreamNames)": [ "string" ],
            "[InputId](API_InputDescription.md#analytics-Type-InputDescription-InputId)": "string",
            "[InputParallelism](API_InputDescription.md#analytics-Type-InputDescription-InputParallelism)": { 
               "[Count](API_InputParallelism.md#analytics-Type-InputParallelism-Count)": number
            },
            "[InputProcessingConfigurationDescription](API_InputDescription.md#analytics-Type-InputDescription-InputProcessingConfigurationDescription)": { 
               "[InputLambdaProcessorDescription](API_InputProcessingConfigurationDescription.md#analytics-Type-InputProcessingConfigurationDescription-InputLambdaProcessorDescription)": { 
                  "[ResourceARN](API_InputLambdaProcessorDescription.md#analytics-Type-InputLambdaProcessorDescription-ResourceARN)": "string",
                  "[RoleARN](API_InputLambdaProcessorDescription.md#analytics-Type-InputLambdaProcessorDescription-RoleARN)": "string"
               }
            },
            "[InputSchema](API_InputDescription.md#analytics-Type-InputDescription-InputSchema)": { 
               "[RecordColumns](API_SourceSchema.md#analytics-Type-SourceSchema-RecordColumns)": [ 
                  { 
                     "[Mapping](API_RecordColumn.md#analytics-Type-RecordColumn-Mapping)": "string",
                     "[Name](API_RecordColumn.md#analytics-Type-RecordColumn-Name)": "string",
                     "[SqlType](API_RecordColumn.md#analytics-Type-RecordColumn-SqlType)": "string"
                  }
               ],
               "[RecordEncoding](API_SourceSchema.md#analytics-Type-SourceSchema-RecordEncoding)": "string",
               "[RecordFormat](API_SourceSchema.md#analytics-Type-SourceSchema-RecordFormat)": { 
                  "[MappingParameters](API_RecordFormat.md#analytics-Type-RecordFormat-MappingParameters)": { 
                     "[CSVMappingParameters](API_MappingParameters.md#analytics-Type-MappingParameters-CSVMappingParameters)": { 
                        "[RecordColumnDelimiter](API_CSVMappingParameters.md#analytics-Type-CSVMappingParameters-RecordColumnDelimiter)": "string",
                        "[RecordRowDelimiter](API_CSVMappingParameters.md#analytics-Type-CSVMappingParameters-RecordRowDelimiter)": "string"
                     },
                     "[JSONMappingParameters](API_MappingParameters.md#analytics-Type-MappingParameters-JSONMappingParameters)": { 
                        "[RecordRowPath](API_JSONMappingParameters.md#analytics-Type-JSONMappingParameters-RecordRowPath)": "string"
                     }
                  },
                  "[RecordFormatType](API_RecordFormat.md#analytics-Type-RecordFormat-RecordFormatType)": "string"
               }
            },
            "[InputStartingPositionConfiguration](API_InputDescription.md#analytics-Type-InputDescription-InputStartingPositionConfiguration)": { 
               "[InputStartingPosition](API_InputStartingPositionConfiguration.md#analytics-Type-InputStartingPositionConfiguration-InputStartingPosition)": "string"
            },
            "[KinesisFirehoseInputDescription](API_InputDescription.md#analytics-Type-InputDescription-KinesisFirehoseInputDescription)": { 
               "[ResourceARN](API_KinesisFirehoseInputDescription.md#analytics-Type-KinesisFirehoseInputDescription-ResourceARN)": "string",
               "[RoleARN](API_KinesisFirehoseInputDescription.md#analytics-Type-KinesisFirehoseInputDescription-RoleARN)": "string"
            },
            "[KinesisStreamsInputDescription](API_InputDescription.md#analytics-Type-InputDescription-KinesisStreamsInputDescription)": { 
               "[ResourceARN](API_KinesisStreamsInputDescription.md#analytics-Type-KinesisStreamsInputDescription-ResourceARN)": "string",
               "[RoleARN](API_KinesisStreamsInputDescription.md#analytics-Type-KinesisStreamsInputDescription-RoleARN)": "string"
            },
            "[NamePrefix](API_InputDescription.md#analytics-Type-InputDescription-NamePrefix)": "string"
         }
      ],
      "[LastUpdateTimestamp](API_ApplicationDetail.md#analytics-Type-ApplicationDetail-LastUpdateTimestamp)": number,
      "[OutputDescriptions](API_ApplicationDetail.md#analytics-Type-ApplicationDetail-OutputDescriptions)": [ 
         { 
            "[DestinationSchema](API_OutputDescription.md#analytics-Type-OutputDescription-DestinationSchema)": { 
               "[RecordFormatType](API_DestinationSchema.md#analytics-Type-DestinationSchema-RecordFormatType)": "string"
            },
            "[KinesisFirehoseOutputDescription](API_OutputDescription.md#analytics-Type-OutputDescription-KinesisFirehoseOutputDescription)": { 
               "[ResourceARN](API_KinesisFirehoseOutputDescription.md#analytics-Type-KinesisFirehoseOutputDescription-ResourceARN)": "string",
               "[RoleARN](API_KinesisFirehoseOutputDescription.md#analytics-Type-KinesisFirehoseOutputDescription-RoleARN)": "string"
            },
            "[KinesisStreamsOutputDescription](API_OutputDescription.md#analytics-Type-OutputDescription-KinesisStreamsOutputDescription)": { 
               "[ResourceARN](API_KinesisStreamsOutputDescription.md#analytics-Type-KinesisStreamsOutputDescription-ResourceARN)": "string",
               "[RoleARN](API_KinesisStreamsOutputDescription.md#analytics-Type-KinesisStreamsOutputDescription-RoleARN)": "string"
            },
            "[LambdaOutputDescription](API_OutputDescription.md#analytics-Type-OutputDescription-LambdaOutputDescription)": { 
               "[ResourceARN](API_LambdaOutputDescription.md#analytics-Type-LambdaOutputDescription-ResourceARN)": "string",
               "[RoleARN](API_LambdaOutputDescription.md#analytics-Type-LambdaOutputDescription-RoleARN)": "string"
            },
            "[Name](API_OutputDescription.md#analytics-Type-OutputDescription-Name)": "string",
            "[OutputId](API_OutputDescription.md#analytics-Type-OutputDescription-OutputId)": "string"
         }
      ],
      "[ReferenceDataSourceDescriptions](API_ApplicationDetail.md#analytics-Type-ApplicationDetail-ReferenceDataSourceDescriptions)": [ 
         { 
            "[ReferenceId](API_ReferenceDataSourceDescription.md#analytics-Type-ReferenceDataSourceDescription-ReferenceId)": "string",
            "[ReferenceSchema](API_ReferenceDataSourceDescription.md#analytics-Type-ReferenceDataSourceDescription-ReferenceSchema)": { 
               "[RecordColumns](API_SourceSchema.md#analytics-Type-SourceSchema-RecordColumns)": [ 
                  { 
                     "[Mapping](API_RecordColumn.md#analytics-Type-RecordColumn-Mapping)": "string",
                     "[Name](API_RecordColumn.md#analytics-Type-RecordColumn-Name)": "string",
                     "[SqlType](API_RecordColumn.md#analytics-Type-RecordColumn-SqlType)": "string"
                  }
               ],
               "[RecordEncoding](API_SourceSchema.md#analytics-Type-SourceSchema-RecordEncoding)": "string",
               "[RecordFormat](API_SourceSchema.md#analytics-Type-SourceSchema-RecordFormat)": { 
                  "[MappingParameters](API_RecordFormat.md#analytics-Type-RecordFormat-MappingParameters)": { 
                     "[CSVMappingParameters](API_MappingParameters.md#analytics-Type-MappingParameters-CSVMappingParameters)": { 
                        "[RecordColumnDelimiter](API_CSVMappingParameters.md#analytics-Type-CSVMappingParameters-RecordColumnDelimiter)": "string",
                        "[RecordRowDelimiter](API_CSVMappingParameters.md#analytics-Type-CSVMappingParameters-RecordRowDelimiter)": "string"
                     },
                     "[JSONMappingParameters](API_MappingParameters.md#analytics-Type-MappingParameters-JSONMappingParameters)": { 
                        "[RecordRowPath](API_JSONMappingParameters.md#analytics-Type-JSONMappingParameters-RecordRowPath)": "string"
                     }
                  },
                  "[RecordFormatType](API_RecordFormat.md#analytics-Type-RecordFormat-RecordFormatType)": "string"
               }
            },
            "[S3ReferenceDataSourceDescription](API_ReferenceDataSourceDescription.md#analytics-Type-ReferenceDataSourceDescription-S3ReferenceDataSourceDescription)": { 
               "[BucketARN](API_S3ReferenceDataSourceDescription.md#analytics-Type-S3ReferenceDataSourceDescription-BucketARN)": "string",
               "[FileKey](API_S3ReferenceDataSourceDescription.md#analytics-Type-S3ReferenceDataSourceDescription-FileKey)": "string",
               "[ReferenceRoleARN](API_S3ReferenceDataSourceDescription.md#analytics-Type-S3ReferenceDataSourceDescription-ReferenceRoleARN)": "string"
            },
            "[TableName](API_ReferenceDataSourceDescription.md#analytics-Type-ReferenceDataSourceDescription-TableName)": "string"
         }
      ]
   }
}
```

## Response Elements<a name="API_DescribeApplication_ResponseElements"></a>

If the action is successful, the service sends back an HTTP 200 response\.

The following data is returned in JSON format by the service\.

 ** [ApplicationDetail](#API_DescribeApplication_ResponseSyntax) **   <a name="analytics-DescribeApplication-response-ApplicationDetail"></a>
Provides a description of the application, such as the application Amazon Resource Name \(ARN\), status, latest version, and input and output configuration details\.  
Type: [ApplicationDetail](API_ApplicationDetail.md) object

## Errors<a name="API_DescribeApplication_Errors"></a>

 **ResourceNotFoundException**   
Specified application can't be found\.  
HTTP Status Code: 400

 **UnsupportedOperationException**   
The request was rejected because a specified parameter is not supported or a specified resource is not valid for this operation\.   
HTTP Status Code: 400

## See Also<a name="API_DescribeApplication_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:
+  [AWS Command Line Interface](https://docs.aws.amazon.com/goto/aws-cli/kinesisanalytics-2015-08-14/DescribeApplication) 
+  [AWS SDK for \.NET](https://docs.aws.amazon.com/goto/DotNetSDKV3/kinesisanalytics-2015-08-14/DescribeApplication) 
+  [AWS SDK for C\+\+](https://docs.aws.amazon.com/goto/SdkForCpp/kinesisanalytics-2015-08-14/DescribeApplication) 
+  [AWS SDK for Go](https://docs.aws.amazon.com/goto/SdkForGoV1/kinesisanalytics-2015-08-14/DescribeApplication) 
+  [AWS SDK for Java](https://docs.aws.amazon.com/goto/SdkForJava/kinesisanalytics-2015-08-14/DescribeApplication) 
+  [AWS SDK for JavaScript](https://docs.aws.amazon.com/goto/AWSJavaScriptSDK/kinesisanalytics-2015-08-14/DescribeApplication) 
+  [AWS SDK for PHP V3](https://docs.aws.amazon.com/goto/SdkForPHPV3/kinesisanalytics-2015-08-14/DescribeApplication) 
+  [AWS SDK for Python](https://docs.aws.amazon.com/goto/boto3/kinesisanalytics-2015-08-14/DescribeApplication) 
+  [AWS SDK for Ruby V3](https://docs.aws.amazon.com/goto/SdkForRubyV3/kinesisanalytics-2015-08-14/DescribeApplication) 