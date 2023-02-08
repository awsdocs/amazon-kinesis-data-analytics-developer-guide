# DescribeApplication<a name="API_DescribeApplication"></a>

**Note**  
This documentation is for version 1 of the Amazon Kinesis Data Analytics API, which only supports SQL applications\. Version 2 of the API supports SQL and Java applications\. For more information about version 2, see [Amazon Kinesis Data Analytics API V2 Documentation](/kinesisanalytics/latest/apiv2/Welcome.html)\.

Returns information about a specific Amazon Kinesis Analytics application\.

If you want to retrieve a list of all applications in your account, use the [ListApplications](https://docs.aws.amazon.com/kinesisanalytics/latest/dev/API_ListApplications.html) operation\.

This operation requires permissions to perform the `kinesisanalytics:DescribeApplication` action\. You can use `DescribeApplication` to get the current application versionId, which you need to call other operations such as `Update`\. 

## Request Syntax<a name="API_DescribeApplication_RequestSyntax"></a>

```
{
   "ApplicationName": "string"
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
   "ApplicationDetail": { 
      "ApplicationARN": "string",
      "ApplicationCode": "string",
      "ApplicationDescription": "string",
      "ApplicationName": "string",
      "ApplicationStatus": "string",
      "ApplicationVersionId": number,
      "CloudWatchLoggingOptionDescriptions": [ 
         { 
            "CloudWatchLoggingOptionId": "string",
            "LogStreamARN": "string",
            "RoleARN": "string"
         }
      ],
      "CreateTimestamp": number,
      "InputDescriptions": [ 
         { 
            "InAppStreamNames": [ "string" ],
            "InputId": "string",
            "InputParallelism": { 
               "Count": number
            },
            "InputProcessingConfigurationDescription": { 
               "InputLambdaProcessorDescription": { 
                  "ResourceARN": "string",
                  "RoleARN": "string"
               }
            },
            "InputSchema": { 
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
            "InputStartingPositionConfiguration": { 
               "InputStartingPosition": "string"
            },
            "KinesisFirehoseInputDescription": { 
               "ResourceARN": "string",
               "RoleARN": "string"
            },
            "KinesisStreamsInputDescription": { 
               "ResourceARN": "string",
               "RoleARN": "string"
            },
            "NamePrefix": "string"
         }
      ],
      "LastUpdateTimestamp": number,
      "OutputDescriptions": [ 
         { 
            "DestinationSchema": { 
               "RecordFormatType": "string"
            },
            "KinesisFirehoseOutputDescription": { 
               "ResourceARN": "string",
               "RoleARN": "string"
            },
            "KinesisStreamsOutputDescription": { 
               "ResourceARN": "string",
               "RoleARN": "string"
            },
            "LambdaOutputDescription": { 
               "ResourceARN": "string",
               "RoleARN": "string"
            },
            "Name": "string",
            "OutputId": "string"
         }
      ],
      "ReferenceDataSourceDescriptions": [ 
         { 
            "ReferenceId": "string",
            "ReferenceSchema": { 
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
            "S3ReferenceDataSourceDescription": { 
               "BucketARN": "string",
               "FileKey": "string",
               "ReferenceRoleARN": "string"
            },
            "TableName": "string"
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

 ** ResourceNotFoundException **   
Specified application can't be found\.  
HTTP Status Code: 400

 ** UnsupportedOperationException **   
The request was rejected because a specified parameter is not supported or a specified resource is not valid for this operation\.   
HTTP Status Code: 400

## See Also<a name="API_DescribeApplication_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:
+  [AWS Command Line Interface](https://docs.aws.amazon.com/goto/aws-cli/kinesisanalytics-2015-08-14/DescribeApplication) 
+  [AWS SDK for \.NET](https://docs.aws.amazon.com/goto/DotNetSDKV3/kinesisanalytics-2015-08-14/DescribeApplication) 
+  [AWS SDK for C\+\+](https://docs.aws.amazon.com/goto/SdkForCpp/kinesisanalytics-2015-08-14/DescribeApplication) 
+  [AWS SDK for Go](https://docs.aws.amazon.com/goto/SdkForGoV1/kinesisanalytics-2015-08-14/DescribeApplication) 
+  [AWS SDK for Java V2](https://docs.aws.amazon.com/goto/SdkForJavaV2/kinesisanalytics-2015-08-14/DescribeApplication) 
+  [AWS SDK for JavaScript](https://docs.aws.amazon.com/goto/AWSJavaScriptSDK/kinesisanalytics-2015-08-14/DescribeApplication) 
+  [AWS SDK for PHP V3](https://docs.aws.amazon.com/goto/SdkForPHPV3/kinesisanalytics-2015-08-14/DescribeApplication) 
+  [AWS SDK for Python](https://docs.aws.amazon.com/goto/boto3/kinesisanalytics-2015-08-14/DescribeApplication) 
+  [AWS SDK for Ruby V3](https://docs.aws.amazon.com/goto/SdkForRubyV3/kinesisanalytics-2015-08-14/DescribeApplication) 