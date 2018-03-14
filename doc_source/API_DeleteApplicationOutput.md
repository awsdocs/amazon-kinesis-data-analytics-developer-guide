# DeleteApplicationOutput<a name="API_DeleteApplicationOutput"></a>

Deletes output destination configuration from your application configuration\. Amazon Kinesis Analytics will no longer write data from the corresponding in\-application stream to the external output destination\.

This operation requires permissions to perform the `kinesisanalytics:DeleteApplicationOutput` action\.

## Request Syntax<a name="API_DeleteApplicationOutput_RequestSyntax"></a>

```
{
   "[ApplicationName](#analytics-DeleteApplicationOutput-request-ApplicationName)": "string",
   "[CurrentApplicationVersionId](#analytics-DeleteApplicationOutput-request-CurrentApplicationVersionId)": number,
   "[OutputId](#analytics-DeleteApplicationOutput-request-OutputId)": "string"
}
```

## Request Parameters<a name="API_DeleteApplicationOutput_RequestParameters"></a>

The request accepts the following data in JSON format\.

 ** [ApplicationName](#API_DeleteApplicationOutput_RequestSyntax) **   <a name="analytics-DeleteApplicationOutput-request-ApplicationName"></a>
Amazon Kinesis Analytics application name\.  
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 128\.  
Pattern: `[a-zA-Z0-9_.-]+`   
Required: Yes

 ** [CurrentApplicationVersionId](#API_DeleteApplicationOutput_RequestSyntax) **   <a name="analytics-DeleteApplicationOutput-request-CurrentApplicationVersionId"></a>
Amazon Kinesis Analytics application version\. You can use the [DescribeApplication](API_DescribeApplication.md) operation to get the current application version\. If the version specified is not the current version, the `ConcurrentModificationException` is returned\.   
Type: Long  
Valid Range: Minimum value of 1\. Maximum value of 999999999\.  
Required: Yes

 ** [OutputId](#API_DeleteApplicationOutput_RequestSyntax) **   <a name="analytics-DeleteApplicationOutput-request-OutputId"></a>
The ID of the configuration to delete\. Each output configuration that is added to the application, either when the application is created or later using the [AddApplicationOutput](API_AddApplicationOutput.md) operation, has a unique ID\. You need to provide the ID to uniquely identify the output configuration that you want to delete from the application configuration\. You can use the [DescribeApplication](API_DescribeApplication.md) operation to get the specific `OutputId`\.   
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 50\.  
Pattern: `[a-zA-Z0-9_.-]+`   
Required: Yes

## Response Elements<a name="API_DeleteApplicationOutput_ResponseElements"></a>

If the action is successful, the service sends back an HTTP 200 response with an empty HTTP body\.

## Errors<a name="API_DeleteApplicationOutput_Errors"></a>

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

## See Also<a name="API_DeleteApplicationOutput_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:

+  [AWS Command Line Interface](http://docs.aws.amazon.com/goto/aws-cli/kinesisanalytics-2015-08-14/DeleteApplicationOutput) 

+  [AWS SDK for \.NET](http://docs.aws.amazon.com/goto/DotNetSDKV3/kinesisanalytics-2015-08-14/DeleteApplicationOutput) 

+  [AWS SDK for C\+\+](http://docs.aws.amazon.com/goto/SdkForCpp/kinesisanalytics-2015-08-14/DeleteApplicationOutput) 

+  [AWS SDK for Go](http://docs.aws.amazon.com/goto/SdkForGoV1/kinesisanalytics-2015-08-14/DeleteApplicationOutput) 

+  [AWS SDK for Java](http://docs.aws.amazon.com/goto/SdkForJava/kinesisanalytics-2015-08-14/DeleteApplicationOutput) 

+  [AWS SDK for JavaScript](http://docs.aws.amazon.com/goto/AWSJavaScriptSDK/kinesisanalytics-2015-08-14/DeleteApplicationOutput) 

+  [AWS SDK for PHP V3](http://docs.aws.amazon.com/goto/SdkForPHPV3/kinesisanalytics-2015-08-14/DeleteApplicationOutput) 

+  [AWS SDK for Python](http://docs.aws.amazon.com/goto/boto3/kinesisanalytics-2015-08-14/DeleteApplicationOutput) 

+  [AWS SDK for Ruby V2](http://docs.aws.amazon.com/goto/SdkForRubyV2/kinesisanalytics-2015-08-14/DeleteApplicationOutput) 