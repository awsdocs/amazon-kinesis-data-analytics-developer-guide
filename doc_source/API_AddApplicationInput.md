# AddApplicationInput<a name="API_AddApplicationInput"></a>

 Adds a streaming source to your Amazon Kinesis application\. For conceptual information, see [Configuring Application Input](http://docs.aws.amazon.com/kinesisanalytics/latest/dev/how-it-works-input.html)\. 

You can add a streaming source either when you create an application or you can use this operation to add a streaming source after you create an application\. For more information, see [CreateApplication](API_CreateApplication.md)\.

Any configuration update, including adding a streaming source using this operation, results in a new version of the application\. You can use the [DescribeApplication](API_DescribeApplication.md) operation to find the current application version\. 

This operation requires permissions to perform the `kinesisanalytics:AddApplicationInput` action\.

## Request Syntax<a name="API_AddApplicationInput_RequestSyntax"></a>

```
{
   "ApplicationName": "string",
   "CurrentApplicationVersionId": number,
   "Input": { 
      "InputParallelism": { 
         "Count": number
      },
      "InputProcessingConfiguration": { 
         "InputLambdaProcessor": { 
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
      "KinesisFirehoseInput": { 
         "ResourceARN": "string",
         "RoleARN": "string"
      },
      "KinesisStreamsInput": { 
         "ResourceARN": "string",
         "RoleARN": "string"
      },
      "NamePrefix": "string"
   }
}
```

## Request Parameters<a name="API_AddApplicationInput_RequestParameters"></a>

The request accepts the following data in JSON format\.

 ** ApplicationName **   
Name of your existing Amazon Kinesis Analytics application to which you want to add the streaming source\.  
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 128\.  
Pattern: `[a-zA-Z0-9_.-]+`   
Required: Yes

 ** CurrentApplicationVersionId **   
Current version of your Amazon Kinesis Analytics application\. You can use the [DescribeApplication](API_DescribeApplication.md) operation to find the current application version\.  
Type: Long  
Valid Range: Minimum value of 1\. Maximum value of 999999999\.  
Required: Yes

 ** Input **   
The [Input](API_Input.md) to add\.  
Type: [Input](API_Input.md) object  
Required: Yes

## Response Elements<a name="API_AddApplicationInput_ResponseElements"></a>

If the action is successful, the service sends back an HTTP 200 response with an empty HTTP body\.

## Errors<a name="API_AddApplicationInput_Errors"></a>

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

## See Also<a name="API_AddApplicationInput_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:

+  [AWS Command Line Interface](http://docs.aws.amazon.com/goto/aws-cli/kinesisanalytics-2015-08-14/AddApplicationInput) 

+  [AWS SDK for \.NET](http://docs.aws.amazon.com/goto/DotNetSDKV3/kinesisanalytics-2015-08-14/AddApplicationInput) 

+  [AWS SDK for C\+\+](http://docs.aws.amazon.com/goto/SdkForCpp/kinesisanalytics-2015-08-14/AddApplicationInput) 

+  [AWS SDK for Go](http://docs.aws.amazon.com/goto/SdkForGoV1/kinesisanalytics-2015-08-14/AddApplicationInput) 

+  [AWS SDK for Java](http://docs.aws.amazon.com/goto/SdkForJava/kinesisanalytics-2015-08-14/AddApplicationInput) 

+  [AWS SDK for JavaScript](http://docs.aws.amazon.com/goto/AWSJavaScriptSDK/kinesisanalytics-2015-08-14/AddApplicationInput) 

+  [AWS SDK for PHP V3](http://docs.aws.amazon.com/goto/SdkForPHPV3/kinesisanalytics-2015-08-14/AddApplicationInput) 

+  [AWS SDK for Python](http://docs.aws.amazon.com/goto/boto3/kinesisanalytics-2015-08-14/AddApplicationInput) 

+  [AWS SDK for Ruby V2](http://docs.aws.amazon.com/goto/SdkForRubyV2/kinesisanalytics-2015-08-14/AddApplicationInput) 