# UpdateApplication<a name="API_UpdateApplication"></a>

**Note**  
This documentation is for version 1 of the Amazon Kinesis Data Analytics API, which only supports SQL applications\. Version 2 of the API supports SQL and Java applications\. For more information about version 2, see [Amazon Kinesis Data Analytics API V2 Documentation](/kinesisanalytics/latest/apiv2/Welcome.html)\.

Updates an existing Amazon Kinesis Analytics application\. Using this API, you can update application code, input configuration, and output configuration\. 

Note that Amazon Kinesis Analytics updates the `CurrentApplicationVersionId` each time you update your application\. 

This operation requires permission for the `kinesisanalytics:UpdateApplication` action\.

## Request Syntax<a name="API_UpdateApplication_RequestSyntax"></a>

```
{
   "[ApplicationName](#analytics-UpdateApplication-request-ApplicationName)": "string",
   "[ApplicationUpdate](#analytics-UpdateApplication-request-ApplicationUpdate)": { 
      "[ApplicationCodeUpdate](API_ApplicationUpdate.md#analytics-Type-ApplicationUpdate-ApplicationCodeUpdate)": "string",
      "[CloudWatchLoggingOptionUpdates](API_ApplicationUpdate.md#analytics-Type-ApplicationUpdate-CloudWatchLoggingOptionUpdates)": [ 
         { 
            "[CloudWatchLoggingOptionId](API_CloudWatchLoggingOptionUpdate.md#analytics-Type-CloudWatchLoggingOptionUpdate-CloudWatchLoggingOptionId)": "string",
            "[LogStreamARNUpdate](API_CloudWatchLoggingOptionUpdate.md#analytics-Type-CloudWatchLoggingOptionUpdate-LogStreamARNUpdate)": "string",
            "[RoleARNUpdate](API_CloudWatchLoggingOptionUpdate.md#analytics-Type-CloudWatchLoggingOptionUpdate-RoleARNUpdate)": "string"
         }
      ],
      "[InputUpdates](API_ApplicationUpdate.md#analytics-Type-ApplicationUpdate-InputUpdates)": [ 
         { 
            "[InputId](API_InputUpdate.md#analytics-Type-InputUpdate-InputId)": "string",
            "[InputParallelismUpdate](API_InputUpdate.md#analytics-Type-InputUpdate-InputParallelismUpdate)": { 
               "[CountUpdate](API_InputParallelismUpdate.md#analytics-Type-InputParallelismUpdate-CountUpdate)": number
            },
            "[InputProcessingConfigurationUpdate](API_InputUpdate.md#analytics-Type-InputUpdate-InputProcessingConfigurationUpdate)": { 
               "[InputLambdaProcessorUpdate](API_InputProcessingConfigurationUpdate.md#analytics-Type-InputProcessingConfigurationUpdate-InputLambdaProcessorUpdate)": { 
                  "[ResourceARNUpdate](API_InputLambdaProcessorUpdate.md#analytics-Type-InputLambdaProcessorUpdate-ResourceARNUpdate)": "string",
                  "[RoleARNUpdate](API_InputLambdaProcessorUpdate.md#analytics-Type-InputLambdaProcessorUpdate-RoleARNUpdate)": "string"
               }
            },
            "[InputSchemaUpdate](API_InputUpdate.md#analytics-Type-InputUpdate-InputSchemaUpdate)": { 
               "[RecordColumnUpdates](API_InputSchemaUpdate.md#analytics-Type-InputSchemaUpdate-RecordColumnUpdates)": [ 
                  { 
                     "[Mapping](API_RecordColumn.md#analytics-Type-RecordColumn-Mapping)": "string",
                     "[Name](API_RecordColumn.md#analytics-Type-RecordColumn-Name)": "string",
                     "[SqlType](API_RecordColumn.md#analytics-Type-RecordColumn-SqlType)": "string"
                  }
               ],
               "[RecordEncodingUpdate](API_InputSchemaUpdate.md#analytics-Type-InputSchemaUpdate-RecordEncodingUpdate)": "string",
               "[RecordFormatUpdate](API_InputSchemaUpdate.md#analytics-Type-InputSchemaUpdate-RecordFormatUpdate)": { 
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
            "[KinesisFirehoseInputUpdate](API_InputUpdate.md#analytics-Type-InputUpdate-KinesisFirehoseInputUpdate)": { 
               "[ResourceARNUpdate](API_KinesisFirehoseInputUpdate.md#analytics-Type-KinesisFirehoseInputUpdate-ResourceARNUpdate)": "string",
               "[RoleARNUpdate](API_KinesisFirehoseInputUpdate.md#analytics-Type-KinesisFirehoseInputUpdate-RoleARNUpdate)": "string"
            },
            "[KinesisStreamsInputUpdate](API_InputUpdate.md#analytics-Type-InputUpdate-KinesisStreamsInputUpdate)": { 
               "[ResourceARNUpdate](API_KinesisStreamsInputUpdate.md#analytics-Type-KinesisStreamsInputUpdate-ResourceARNUpdate)": "string",
               "[RoleARNUpdate](API_KinesisStreamsInputUpdate.md#analytics-Type-KinesisStreamsInputUpdate-RoleARNUpdate)": "string"
            },
            "[NamePrefixUpdate](API_InputUpdate.md#analytics-Type-InputUpdate-NamePrefixUpdate)": "string"
         }
      ],
      "[OutputUpdates](API_ApplicationUpdate.md#analytics-Type-ApplicationUpdate-OutputUpdates)": [ 
         { 
            "[DestinationSchemaUpdate](API_OutputUpdate.md#analytics-Type-OutputUpdate-DestinationSchemaUpdate)": { 
               "[RecordFormatType](API_DestinationSchema.md#analytics-Type-DestinationSchema-RecordFormatType)": "string"
            },
            "[KinesisFirehoseOutputUpdate](API_OutputUpdate.md#analytics-Type-OutputUpdate-KinesisFirehoseOutputUpdate)": { 
               "[ResourceARNUpdate](API_KinesisFirehoseOutputUpdate.md#analytics-Type-KinesisFirehoseOutputUpdate-ResourceARNUpdate)": "string",
               "[RoleARNUpdate](API_KinesisFirehoseOutputUpdate.md#analytics-Type-KinesisFirehoseOutputUpdate-RoleARNUpdate)": "string"
            },
            "[KinesisStreamsOutputUpdate](API_OutputUpdate.md#analytics-Type-OutputUpdate-KinesisStreamsOutputUpdate)": { 
               "[ResourceARNUpdate](API_KinesisStreamsOutputUpdate.md#analytics-Type-KinesisStreamsOutputUpdate-ResourceARNUpdate)": "string",
               "[RoleARNUpdate](API_KinesisStreamsOutputUpdate.md#analytics-Type-KinesisStreamsOutputUpdate-RoleARNUpdate)": "string"
            },
            "[LambdaOutputUpdate](API_OutputUpdate.md#analytics-Type-OutputUpdate-LambdaOutputUpdate)": { 
               "[ResourceARNUpdate](API_LambdaOutputUpdate.md#analytics-Type-LambdaOutputUpdate-ResourceARNUpdate)": "string",
               "[RoleARNUpdate](API_LambdaOutputUpdate.md#analytics-Type-LambdaOutputUpdate-RoleARNUpdate)": "string"
            },
            "[NameUpdate](API_OutputUpdate.md#analytics-Type-OutputUpdate-NameUpdate)": "string",
            "[OutputId](API_OutputUpdate.md#analytics-Type-OutputUpdate-OutputId)": "string"
         }
      ],
      "[ReferenceDataSourceUpdates](API_ApplicationUpdate.md#analytics-Type-ApplicationUpdate-ReferenceDataSourceUpdates)": [ 
         { 
            "[ReferenceId](API_ReferenceDataSourceUpdate.md#analytics-Type-ReferenceDataSourceUpdate-ReferenceId)": "string",
            "[ReferenceSchemaUpdate](API_ReferenceDataSourceUpdate.md#analytics-Type-ReferenceDataSourceUpdate-ReferenceSchemaUpdate)": { 
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
            "[S3ReferenceDataSourceUpdate](API_ReferenceDataSourceUpdate.md#analytics-Type-ReferenceDataSourceUpdate-S3ReferenceDataSourceUpdate)": { 
               "[BucketARNUpdate](API_S3ReferenceDataSourceUpdate.md#analytics-Type-S3ReferenceDataSourceUpdate-BucketARNUpdate)": "string",
               "[FileKeyUpdate](API_S3ReferenceDataSourceUpdate.md#analytics-Type-S3ReferenceDataSourceUpdate-FileKeyUpdate)": "string",
               "[ReferenceRoleARNUpdate](API_S3ReferenceDataSourceUpdate.md#analytics-Type-S3ReferenceDataSourceUpdate-ReferenceRoleARNUpdate)": "string"
            },
            "[TableNameUpdate](API_ReferenceDataSourceUpdate.md#analytics-Type-ReferenceDataSourceUpdate-TableNameUpdate)": "string"
         }
      ]
   },
   "[CurrentApplicationVersionId](#analytics-UpdateApplication-request-CurrentApplicationVersionId)": number
}
```

## Request Parameters<a name="API_UpdateApplication_RequestParameters"></a>

The request accepts the following data in JSON format\.

 ** [ApplicationName](#API_UpdateApplication_RequestSyntax) **   <a name="analytics-UpdateApplication-request-ApplicationName"></a>
Name of the Amazon Kinesis Analytics application to update\.  
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 128\.  
Pattern: `[a-zA-Z0-9_.-]+`   
Required: Yes

 ** [ApplicationUpdate](#API_UpdateApplication_RequestSyntax) **   <a name="analytics-UpdateApplication-request-ApplicationUpdate"></a>
Describes application updates\.  
Type: [ApplicationUpdate](API_ApplicationUpdate.md) object  
Required: Yes

 ** [CurrentApplicationVersionId](#API_UpdateApplication_RequestSyntax) **   <a name="analytics-UpdateApplication-request-CurrentApplicationVersionId"></a>
The current application version ID\. You can use the [DescribeApplication](https://docs.aws.amazon.com/kinesisanalytics/latest/dev/API_DescribeApplication.html) operation to get this value\.  
Type: Long  
Valid Range: Minimum value of 1\. Maximum value of 999999999\.  
Required: Yes

## Response Elements<a name="API_UpdateApplication_ResponseElements"></a>

If the action is successful, the service sends back an HTTP 200 response with an empty HTTP body\.

## Errors<a name="API_UpdateApplication_Errors"></a>

 **CodeValidationException**   
User\-provided application code \(query\) is invalid\. This can be a simple syntax error\.  
HTTP Status Code: 400

 **ConcurrentModificationException**   
Exception thrown as a result of concurrent modification to an application\. For example, two individuals attempting to edit the same application at the same time\.  
HTTP Status Code: 400

 **InvalidArgumentException**   
Specified input parameter value is invalid\.  
HTTP Status Code: 400

 **ResourceInUseException**   
Application is not available for this operation\.  
HTTP Status Code: 400

 **ResourceNotFoundException**   
Specified application can't be found\.  
HTTP Status Code: 400

 **UnsupportedOperationException**   
The request was rejected because a specified parameter is not supported or a specified resource is not valid for this operation\.   
HTTP Status Code: 400

## See Also<a name="API_UpdateApplication_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:
+  [AWS Command Line Interface](https://docs.aws.amazon.com/goto/aws-cli/kinesisanalytics-2015-08-14/UpdateApplication) 
+  [AWS SDK for \.NET](https://docs.aws.amazon.com/goto/DotNetSDKV3/kinesisanalytics-2015-08-14/UpdateApplication) 
+  [AWS SDK for C\+\+](https://docs.aws.amazon.com/goto/SdkForCpp/kinesisanalytics-2015-08-14/UpdateApplication) 
+  [AWS SDK for Go](https://docs.aws.amazon.com/goto/SdkForGoV1/kinesisanalytics-2015-08-14/UpdateApplication) 
+  [AWS SDK for Java](https://docs.aws.amazon.com/goto/SdkForJava/kinesisanalytics-2015-08-14/UpdateApplication) 
+  [AWS SDK for JavaScript](https://docs.aws.amazon.com/goto/AWSJavaScriptSDK/kinesisanalytics-2015-08-14/UpdateApplication) 
+  [AWS SDK for PHP V3](https://docs.aws.amazon.com/goto/SdkForPHPV3/kinesisanalytics-2015-08-14/UpdateApplication) 
+  [AWS SDK for Python](https://docs.aws.amazon.com/goto/boto3/kinesisanalytics-2015-08-14/UpdateApplication) 
+  [AWS SDK for Ruby V3](https://docs.aws.amazon.com/goto/SdkForRubyV3/kinesisanalytics-2015-08-14/UpdateApplication) 