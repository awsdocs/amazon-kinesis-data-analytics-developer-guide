# UpdateApplication<a name="API_UpdateApplication"></a>

**Note**  
This documentation is for version 1 of the Amazon Kinesis Data Analytics API, which only supports SQL applications\. Version 2 of the API supports SQL and Java applications\. For more information about version 2, see [Amazon Kinesis Data Analytics API V2 Documentation](/kinesisanalytics/latest/apiv2/Welcome.html)\.

Updates an existing Amazon Kinesis Analytics application\. Using this API, you can update application code, input configuration, and output configuration\. 

Note that Amazon Kinesis Analytics updates the `CurrentApplicationVersionId` each time you update your application\. 

This operation requires permission for the `kinesisanalytics:UpdateApplication` action\.

## Request Syntax<a name="API_UpdateApplication_RequestSyntax"></a>

```
{
   "ApplicationName": "string",
   "ApplicationUpdate": { 
      "ApplicationCodeUpdate": "string",
      "CloudWatchLoggingOptionUpdates": [ 
         { 
            "CloudWatchLoggingOptionId": "string",
            "LogStreamARNUpdate": "string",
            "RoleARNUpdate": "string"
         }
      ],
      "InputUpdates": [ 
         { 
            "InputId": "string",
            "InputParallelismUpdate": { 
               "CountUpdate": number
            },
            "InputProcessingConfigurationUpdate": { 
               "InputLambdaProcessorUpdate": { 
                  "ResourceARNUpdate": "string",
                  "RoleARNUpdate": "string"
               }
            },
            "InputSchemaUpdate": { 
               "RecordColumnUpdates": [ 
                  { 
                     "Mapping": "string",
                     "Name": "string",
                     "SqlType": "string"
                  }
               ],
               "RecordEncodingUpdate": "string",
               "RecordFormatUpdate": { 
                  "MappingParameters": { 
                     "CSVMappingParameters": { 
                        "RecordColumnDelimiter": "string",
                        "RecordRowDelimiter": "string"
                     },
                     "JSONMappingParameters": { 
                        "RecordRowPath": "string"
                     }
                  },
                  "RecordFormatType": "string"
               }
            },
            "KinesisFirehoseInputUpdate": { 
               "ResourceARNUpdate": "string",
               "RoleARNUpdate": "string"
            },
            "KinesisStreamsInputUpdate": { 
               "ResourceARNUpdate": "string",
               "RoleARNUpdate": "string"
            },
            "NamePrefixUpdate": "string"
         }
      ],
      "OutputUpdates": [ 
         { 
            "DestinationSchemaUpdate": { 
               "RecordFormatType": "string"
            },
            "KinesisFirehoseOutputUpdate": { 
               "ResourceARNUpdate": "string",
               "RoleARNUpdate": "string"
            },
            "KinesisStreamsOutputUpdate": { 
               "ResourceARNUpdate": "string",
               "RoleARNUpdate": "string"
            },
            "LambdaOutputUpdate": { 
               "ResourceARNUpdate": "string",
               "RoleARNUpdate": "string"
            },
            "NameUpdate": "string",
            "OutputId": "string"
         }
      ],
      "ReferenceDataSourceUpdates": [ 
         { 
            "ReferenceId": "string",
            "ReferenceSchemaUpdate": { 
               "RecordColumns": [ 
                  { 
                     "Mapping": "string",
                     "Name": "string",
                     "SqlType": "string"
                  }
               ],
               "RecordEncoding": "string",
               "RecordFormat": { 
                  "MappingParameters": { 
                     "CSVMappingParameters": { 
                        "RecordColumnDelimiter": "string",
                        "RecordRowDelimiter": "string"
                     },
                     "JSONMappingParameters": { 
                        "RecordRowPath": "string"
                     }
                  },
                  "RecordFormatType": "string"
               }
            },
            "S3ReferenceDataSourceUpdate": { 
               "BucketARNUpdate": "string",
               "FileKeyUpdate": "string",
               "ReferenceRoleARNUpdate": "string"
            },
            "TableNameUpdate": "string"
         }
      ]
   },
   "CurrentApplicationVersionId": number
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

 ** CodeValidationException **   
User\-provided application code \(query\) is invalid\. This can be a simple syntax error\.  
HTTP Status Code: 400

 ** ConcurrentModificationException **   
Exception thrown as a result of concurrent modification to an application\. For example, two individuals attempting to edit the same application at the same time\.  
HTTP Status Code: 400

 ** InvalidArgumentException **   
Specified input parameter value is invalid\.  
HTTP Status Code: 400

 ** ResourceInUseException **   
Application is not available for this operation\.  
HTTP Status Code: 400

 ** ResourceNotFoundException **   
Specified application can't be found\.  
HTTP Status Code: 400

 ** UnsupportedOperationException **   
The request was rejected because a specified parameter is not supported or a specified resource is not valid for this operation\.   
HTTP Status Code: 400

## See Also<a name="API_UpdateApplication_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:
+  [AWS Command Line Interface](https://docs.aws.amazon.com/goto/aws-cli/kinesisanalytics-2015-08-14/UpdateApplication) 
+  [AWS SDK for \.NET](https://docs.aws.amazon.com/goto/DotNetSDKV3/kinesisanalytics-2015-08-14/UpdateApplication) 
+  [AWS SDK for C\+\+](https://docs.aws.amazon.com/goto/SdkForCpp/kinesisanalytics-2015-08-14/UpdateApplication) 
+  [AWS SDK for Go](https://docs.aws.amazon.com/goto/SdkForGoV1/kinesisanalytics-2015-08-14/UpdateApplication) 
+  [AWS SDK for Java V2](https://docs.aws.amazon.com/goto/SdkForJavaV2/kinesisanalytics-2015-08-14/UpdateApplication) 
+  [AWS SDK for JavaScript](https://docs.aws.amazon.com/goto/AWSJavaScriptSDK/kinesisanalytics-2015-08-14/UpdateApplication) 
+  [AWS SDK for PHP V3](https://docs.aws.amazon.com/goto/SdkForPHPV3/kinesisanalytics-2015-08-14/UpdateApplication) 
+  [AWS SDK for Python](https://docs.aws.amazon.com/goto/boto3/kinesisanalytics-2015-08-14/UpdateApplication) 
+  [AWS SDK for Ruby V3](https://docs.aws.amazon.com/goto/SdkForRubyV3/kinesisanalytics-2015-08-14/UpdateApplication) 