# AddApplicationInputProcessingConfiguration<a name="API_AddApplicationInputProcessingConfiguration"></a>

Adds an [InputProcessingConfiguration](API_InputProcessingConfiguration.md) to an application\. An input processor preprocesses records on the input stream before the application's SQL code executes\. Currently, the only input processor available is [AWS Lambda](https://aws.amazon.com/documentation/lambda/)\.

## Request Syntax<a name="API_AddApplicationInputProcessingConfiguration_RequestSyntax"></a>

```
{
   "ApplicationName": "string",
   "CurrentApplicationVersionId": number,
   "InputId": "string",
   "InputProcessingConfiguration": { 
      "InputLambdaProcessor": { 
         "ResourceARN": "string",
         "RoleARN": "string"
      }
   }
}
```

## Request Parameters<a name="API_AddApplicationInputProcessingConfiguration_RequestParameters"></a>

The request accepts the following data in JSON format\.

 ** ApplicationName **   
Name of the application to which you want to add the input processing configuration\.  
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 128\.  
Pattern: `[a-zA-Z0-9_.-]+`   
Required: Yes

 ** CurrentApplicationVersionId **   
Version of the application to which you want to add the input processing configuration\. You can use the [DescribeApplication](API_DescribeApplication.md) operation to get the current application version\. If the version specified is not the current version, the `ConcurrentModificationException` is returned\.  
Type: Long  
Valid Range: Minimum value of 1\. Maximum value of 999999999\.  
Required: Yes

 ** InputId **   
The ID of the input configuration to add the input processing configuration to\. You can get a list of the input IDs for an application using the [DescribeApplication](API_DescribeApplication.md) operation\.  
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 50\.  
Pattern: `[a-zA-Z0-9_.-]+`   
Required: Yes

 ** InputProcessingConfiguration **   
The [InputProcessingConfiguration](API_InputProcessingConfiguration.md) to add to the application\.  
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

## See Also<a name="API_AddApplicationInputProcessingConfiguration_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:

+  [AWS Command Line Interface](http://docs.aws.amazon.com/goto/aws-cli/kinesisanalytics-2015-08-14/AddApplicationInputProcessingConfiguration) 

+  [AWS SDK for \.NET](http://docs.aws.amazon.com/goto/DotNetSDKV3/kinesisanalytics-2015-08-14/AddApplicationInputProcessingConfiguration) 

+  [AWS SDK for C\+\+](http://docs.aws.amazon.com/goto/SdkForCpp/kinesisanalytics-2015-08-14/AddApplicationInputProcessingConfiguration) 

+  [AWS SDK for Go](http://docs.aws.amazon.com/goto/SdkForGoV1/kinesisanalytics-2015-08-14/AddApplicationInputProcessingConfiguration) 

+  [AWS SDK for Java](http://docs.aws.amazon.com/goto/SdkForJava/kinesisanalytics-2015-08-14/AddApplicationInputProcessingConfiguration) 

+  [AWS SDK for JavaScript](http://docs.aws.amazon.com/goto/AWSJavaScriptSDK/kinesisanalytics-2015-08-14/AddApplicationInputProcessingConfiguration) 

+  [AWS SDK for PHP V3](http://docs.aws.amazon.com/goto/SdkForPHPV3/kinesisanalytics-2015-08-14/AddApplicationInputProcessingConfiguration) 

+  [AWS SDK for Python](http://docs.aws.amazon.com/goto/boto3/kinesisanalytics-2015-08-14/AddApplicationInputProcessingConfiguration) 

+  [AWS SDK for Ruby V2](http://docs.aws.amazon.com/goto/SdkForRubyV2/kinesisanalytics-2015-08-14/AddApplicationInputProcessingConfiguration) 