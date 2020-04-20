# AddApplicationInputProcessingConfiguration<a name="API_AddApplicationInputProcessingConfiguration"></a>

**Note**  
This documentation is for version 1 of the Amazon Kinesis Data Analytics API, which only supports SQL applications\. Version 2 of the API supports SQL and Java applications\. For more information about version 2, see [Amazon Kinesis Data Analytics API V2 Documentation](/kinesisanalytics/latest/apiv2/Welcome.html)\.

Adds an [InputProcessingConfiguration](https://docs.aws.amazon.com/kinesisanalytics/latest/dev/API_InputProcessingConfiguration.html) to an application\. An input processor preprocesses records on the input stream before the application's SQL code executes\. Currently, the only input processor available is [AWS Lambda](https://docs.aws.amazon.com/lambda/)\.

## Request Syntax<a name="API_AddApplicationInputProcessingConfiguration_RequestSyntax"></a>

```
{
   "[ApplicationName](#analytics-AddApplicationInputProcessingConfiguration-request-ApplicationName)": "string",
   "[CurrentApplicationVersionId](#analytics-AddApplicationInputProcessingConfiguration-request-CurrentApplicationVersionId)": number,
   "[InputId](#analytics-AddApplicationInputProcessingConfiguration-request-InputId)": "string",
   "[InputProcessingConfiguration](#analytics-AddApplicationInputProcessingConfiguration-request-InputProcessingConfiguration)": { 
      "[InputLambdaProcessor](API_InputProcessingConfiguration.md#analytics-Type-InputProcessingConfiguration-InputLambdaProcessor)": { 
         "[ResourceARN](API_InputLambdaProcessor.md#analytics-Type-InputLambdaProcessor-ResourceARN)": "string",
         "[RoleARN](API_InputLambdaProcessor.md#analytics-Type-InputLambdaProcessor-RoleARN)": "string"
      }
   }
}
```

## Request Parameters<a name="API_AddApplicationInputProcessingConfiguration_RequestParameters"></a>

The request accepts the following data in JSON format\.

 ** [ApplicationName](#API_AddApplicationInputProcessingConfiguration_RequestSyntax) **   <a name="analytics-AddApplicationInputProcessingConfiguration-request-ApplicationName"></a>
Name of the application to which you want to add the input processing configuration\.  
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 128\.  
Pattern: `[a-zA-Z0-9_.-]+`   
Required: Yes

 ** [CurrentApplicationVersionId](#API_AddApplicationInputProcessingConfiguration_RequestSyntax) **   <a name="analytics-AddApplicationInputProcessingConfiguration-request-CurrentApplicationVersionId"></a>
Version of the application to which you want to add the input processing configuration\. You can use the [DescribeApplication](https://docs.aws.amazon.com/kinesisanalytics/latest/dev/API_DescribeApplication.html) operation to get the current application version\. If the version specified is not the current version, the `ConcurrentModificationException` is returned\.  
Type: Long  
Valid Range: Minimum value of 1\. Maximum value of 999999999\.  
Required: Yes

 ** [InputId](#API_AddApplicationInputProcessingConfiguration_RequestSyntax) **   <a name="analytics-AddApplicationInputProcessingConfiguration-request-InputId"></a>
The ID of the input configuration to add the input processing configuration to\. You can get a list of the input IDs for an application using the [DescribeApplication](https://docs.aws.amazon.com/kinesisanalytics/latest/dev/API_DescribeApplication.html) operation\.  
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 50\.  
Pattern: `[a-zA-Z0-9_.-]+`   
Required: Yes

 ** [InputProcessingConfiguration](#API_AddApplicationInputProcessingConfiguration_RequestSyntax) **   <a name="analytics-AddApplicationInputProcessingConfiguration-request-InputProcessingConfiguration"></a>
The [InputProcessingConfiguration](https://docs.aws.amazon.com/kinesisanalytics/latest/dev/API_InputProcessingConfiguration.html) to add to the application\.  
Type: [InputProcessingConfiguration](API_InputProcessingConfiguration.md) object  
Required: Yes

## Response Elements<a name="API_AddApplicationInputProcessingConfiguration_ResponseElements"></a>

If the action is successful, the service sends back an HTTP 200 response with an empty HTTP body\.

## Errors<a name="API_AddApplicationInputProcessingConfiguration_Errors"></a>

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

## See Also<a name="API_AddApplicationInputProcessingConfiguration_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:
+  [AWS Command Line Interface](https://docs.aws.amazon.com/goto/aws-cli/kinesisanalytics-2015-08-14/AddApplicationInputProcessingConfiguration) 
+  [AWS SDK for \.NET](https://docs.aws.amazon.com/goto/DotNetSDKV3/kinesisanalytics-2015-08-14/AddApplicationInputProcessingConfiguration) 
+  [AWS SDK for C\+\+](https://docs.aws.amazon.com/goto/SdkForCpp/kinesisanalytics-2015-08-14/AddApplicationInputProcessingConfiguration) 
+  [AWS SDK for Go](https://docs.aws.amazon.com/goto/SdkForGoV1/kinesisanalytics-2015-08-14/AddApplicationInputProcessingConfiguration) 
+  [AWS SDK for Java](https://docs.aws.amazon.com/goto/SdkForJava/kinesisanalytics-2015-08-14/AddApplicationInputProcessingConfiguration) 
+  [AWS SDK for JavaScript](https://docs.aws.amazon.com/goto/AWSJavaScriptSDK/kinesisanalytics-2015-08-14/AddApplicationInputProcessingConfiguration) 
+  [AWS SDK for PHP V3](https://docs.aws.amazon.com/goto/SdkForPHPV3/kinesisanalytics-2015-08-14/AddApplicationInputProcessingConfiguration) 
+  [AWS SDK for Python](https://docs.aws.amazon.com/goto/boto3/kinesisanalytics-2015-08-14/AddApplicationInputProcessingConfiguration) 
+  [AWS SDK for Ruby V3](https://docs.aws.amazon.com/goto/SdkForRubyV3/kinesisanalytics-2015-08-14/AddApplicationInputProcessingConfiguration) 