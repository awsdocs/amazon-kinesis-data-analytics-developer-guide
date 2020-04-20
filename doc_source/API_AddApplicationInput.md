# AddApplicationInput<a name="API_AddApplicationInput"></a>

**Note**  
This documentation is for version 1 of the Amazon Kinesis Data Analytics API, which only supports SQL applications\. Version 2 of the API supports SQL and Java applications\. For more information about version 2, see [Amazon Kinesis Data Analytics API V2 Documentation](/kinesisanalytics/latest/apiv2/Welcome.html)\.

 Adds a streaming source to your Amazon Kinesis application\. For conceptual information, see [Configuring Application Input](https://docs.aws.amazon.com/kinesisanalytics/latest/dev/how-it-works-input.html)\. 

You can add a streaming source either when you create an application or you can use this operation to add a streaming source after you create an application\. For more information, see [CreateApplication](https://docs.aws.amazon.com/kinesisanalytics/latest/dev/API_CreateApplication.html)\.

Any configuration update, including adding a streaming source using this operation, results in a new version of the application\. You can use the [DescribeApplication](https://docs.aws.amazon.com/kinesisanalytics/latest/dev/API_DescribeApplication.html) operation to find the current application version\. 

This operation requires permissions to perform the `kinesisanalytics:AddApplicationInput` action\.

## Request Syntax<a name="API_AddApplicationInput_RequestSyntax"></a>

```
{
   "[ApplicationName](#analytics-AddApplicationInput-request-ApplicationName)": "string",
   "[CurrentApplicationVersionId](#analytics-AddApplicationInput-request-CurrentApplicationVersionId)": number,
   "[Input](#analytics-AddApplicationInput-request-Input)": { 
      "[InputParallelism](API_Input.md#analytics-Type-Input-InputParallelism)": { 
         "[Count](API_InputParallelism.md#analytics-Type-InputParallelism-Count)": number
      },
      "[InputProcessingConfiguration](API_Input.md#analytics-Type-Input-InputProcessingConfiguration)": { 
         "[InputLambdaProcessor](API_InputProcessingConfiguration.md#analytics-Type-InputProcessingConfiguration-InputLambdaProcessor)": { 
            "[ResourceARN](API_InputLambdaProcessor.md#analytics-Type-InputLambdaProcessor-ResourceARN)": "string",
            "[RoleARN](API_InputLambdaProcessor.md#analytics-Type-InputLambdaProcessor-RoleARN)": "string"
         }
      },
      "[InputSchema](API_Input.md#analytics-Type-Input-InputSchema)": { 
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
      "[KinesisFirehoseInput](API_Input.md#analytics-Type-Input-KinesisFirehoseInput)": { 
         "[ResourceARN](API_KinesisFirehoseInput.md#analytics-Type-KinesisFirehoseInput-ResourceARN)": "string",
         "[RoleARN](API_KinesisFirehoseInput.md#analytics-Type-KinesisFirehoseInput-RoleARN)": "string"
      },
      "[KinesisStreamsInput](API_Input.md#analytics-Type-Input-KinesisStreamsInput)": { 
         "[ResourceARN](API_KinesisStreamsInput.md#analytics-Type-KinesisStreamsInput-ResourceARN)": "string",
         "[RoleARN](API_KinesisStreamsInput.md#analytics-Type-KinesisStreamsInput-RoleARN)": "string"
      },
      "[NamePrefix](API_Input.md#analytics-Type-Input-NamePrefix)": "string"
   }
}
```

## Request Parameters<a name="API_AddApplicationInput_RequestParameters"></a>

The request accepts the following data in JSON format\.

 ** [ApplicationName](#API_AddApplicationInput_RequestSyntax) **   <a name="analytics-AddApplicationInput-request-ApplicationName"></a>
Name of your existing Amazon Kinesis Analytics application to which you want to add the streaming source\.  
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 128\.  
Pattern: `[a-zA-Z0-9_.-]+`   
Required: Yes

 ** [CurrentApplicationVersionId](#API_AddApplicationInput_RequestSyntax) **   <a name="analytics-AddApplicationInput-request-CurrentApplicationVersionId"></a>
Current version of your Amazon Kinesis Analytics application\. You can use the [DescribeApplication](https://docs.aws.amazon.com/kinesisanalytics/latest/dev/API_DescribeApplication.html) operation to find the current application version\.  
Type: Long  
Valid Range: Minimum value of 1\. Maximum value of 999999999\.  
Required: Yes

 ** [Input](#API_AddApplicationInput_RequestSyntax) **   <a name="analytics-AddApplicationInput-request-Input"></a>
The [Input](https://docs.aws.amazon.com/kinesisanalytics/latest/dev/API_Input.html) to add\.  
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

 **UnsupportedOperationException**   
The request was rejected because a specified parameter is not supported or a specified resource is not valid for this operation\.   
HTTP Status Code: 400

## See Also<a name="API_AddApplicationInput_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:
+  [AWS Command Line Interface](https://docs.aws.amazon.com/goto/aws-cli/kinesisanalytics-2015-08-14/AddApplicationInput) 
+  [AWS SDK for \.NET](https://docs.aws.amazon.com/goto/DotNetSDKV3/kinesisanalytics-2015-08-14/AddApplicationInput) 
+  [AWS SDK for C\+\+](https://docs.aws.amazon.com/goto/SdkForCpp/kinesisanalytics-2015-08-14/AddApplicationInput) 
+  [AWS SDK for Go](https://docs.aws.amazon.com/goto/SdkForGoV1/kinesisanalytics-2015-08-14/AddApplicationInput) 
+  [AWS SDK for Java](https://docs.aws.amazon.com/goto/SdkForJava/kinesisanalytics-2015-08-14/AddApplicationInput) 
+  [AWS SDK for JavaScript](https://docs.aws.amazon.com/goto/AWSJavaScriptSDK/kinesisanalytics-2015-08-14/AddApplicationInput) 
+  [AWS SDK for PHP V3](https://docs.aws.amazon.com/goto/SdkForPHPV3/kinesisanalytics-2015-08-14/AddApplicationInput) 
+  [AWS SDK for Python](https://docs.aws.amazon.com/goto/boto3/kinesisanalytics-2015-08-14/AddApplicationInput) 
+  [AWS SDK for Ruby V3](https://docs.aws.amazon.com/goto/SdkForRubyV3/kinesisanalytics-2015-08-14/AddApplicationInput) 