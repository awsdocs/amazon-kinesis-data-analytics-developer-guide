# DeleteApplicationInputProcessingConfiguration<a name="API_DeleteApplicationInputProcessingConfiguration"></a>

Deletes an [InputProcessingConfiguration](API_InputProcessingConfiguration.md) from an input\.

## Request Syntax<a name="API_DeleteApplicationInputProcessingConfiguration_RequestSyntax"></a>

```
{
   "[ApplicationName](#analytics-DeleteApplicationInputProcessingConfiguration-request-ApplicationName)": "string",
   "[CurrentApplicationVersionId](#analytics-DeleteApplicationInputProcessingConfiguration-request-CurrentApplicationVersionId)": number,
   "[InputId](#analytics-DeleteApplicationInputProcessingConfiguration-request-InputId)": "string"
}
```

## Request Parameters<a name="API_DeleteApplicationInputProcessingConfiguration_RequestParameters"></a>

The request accepts the following data in JSON format\.

 ** [ApplicationName](#API_DeleteApplicationInputProcessingConfiguration_RequestSyntax) **   <a name="analytics-DeleteApplicationInputProcessingConfiguration-request-ApplicationName"></a>
The Kinesis Analytics application name\.  
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 128\.  
Pattern: `[a-zA-Z0-9_.-]+`   
Required: Yes

 ** [CurrentApplicationVersionId](#API_DeleteApplicationInputProcessingConfiguration_RequestSyntax) **   <a name="analytics-DeleteApplicationInputProcessingConfiguration-request-CurrentApplicationVersionId"></a>
The version ID of the Kinesis Analytics application\.  
Type: Long  
Valid Range: Minimum value of 1\. Maximum value of 999999999\.  
Required: Yes

 ** [InputId](#API_DeleteApplicationInputProcessingConfiguration_RequestSyntax) **   <a name="analytics-DeleteApplicationInputProcessingConfiguration-request-InputId"></a>
The ID of the input configuration from which to delete the input processing configuration\. You can get a list of the input IDs for an application by using the [DescribeApplication](API_DescribeApplication.md) operation\.  
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 50\.  
Pattern: `[a-zA-Z0-9_.-]+`   
Required: Yes

## Response Elements<a name="API_DeleteApplicationInputProcessingConfiguration_ResponseElements"></a>

If the action is successful, the service sends back an HTTP 200 response with an empty HTTP body\.

## Errors<a name="API_DeleteApplicationInputProcessingConfiguration_Errors"></a>

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

## See Also<a name="API_DeleteApplicationInputProcessingConfiguration_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:

+  [AWS Command Line Interface](http://docs.aws.amazon.com/goto/aws-cli/kinesisanalytics-2015-08-14/DeleteApplicationInputProcessingConfiguration) 

+  [AWS SDK for \.NET](http://docs.aws.amazon.com/goto/DotNetSDKV3/kinesisanalytics-2015-08-14/DeleteApplicationInputProcessingConfiguration) 

+  [AWS SDK for C\+\+](http://docs.aws.amazon.com/goto/SdkForCpp/kinesisanalytics-2015-08-14/DeleteApplicationInputProcessingConfiguration) 

+  [AWS SDK for Go](http://docs.aws.amazon.com/goto/SdkForGoV1/kinesisanalytics-2015-08-14/DeleteApplicationInputProcessingConfiguration) 

+  [AWS SDK for Java](http://docs.aws.amazon.com/goto/SdkForJava/kinesisanalytics-2015-08-14/DeleteApplicationInputProcessingConfiguration) 

+  [AWS SDK for JavaScript](http://docs.aws.amazon.com/goto/AWSJavaScriptSDK/kinesisanalytics-2015-08-14/DeleteApplicationInputProcessingConfiguration) 

+  [AWS SDK for PHP V3](http://docs.aws.amazon.com/goto/SdkForPHPV3/kinesisanalytics-2015-08-14/DeleteApplicationInputProcessingConfiguration) 

+  [AWS SDK for Python](http://docs.aws.amazon.com/goto/boto3/kinesisanalytics-2015-08-14/DeleteApplicationInputProcessingConfiguration) 

+  [AWS SDK for Ruby V2](http://docs.aws.amazon.com/goto/SdkForRubyV2/kinesisanalytics-2015-08-14/DeleteApplicationInputProcessingConfiguration) 