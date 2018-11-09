# StopApplication<a name="API_StopApplication"></a>

Stops the application from processing input data\. You can stop an application only if it is in the running state\. You can use the [DescribeApplication](API_DescribeApplication.md) operation to find the application state\. After the application is stopped, Amazon Kinesis Analytics stops reading data from the input, the application stops processing data, and there is no output written to the destination\. 

This operation requires permissions to perform the `kinesisanalytics:StopApplication` action\.

## Request Syntax<a name="API_StopApplication_RequestSyntax"></a>

```
{
   "[ApplicationName](#analytics-StopApplication-request-ApplicationName)": "string"
}
```

## Request Parameters<a name="API_StopApplication_RequestParameters"></a>

The request accepts the following data in JSON format\.

 ** [ApplicationName](#API_StopApplication_RequestSyntax) **   <a name="analytics-StopApplication-request-ApplicationName"></a>
Name of the running application to stop\.  
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 128\.  
Pattern: `[a-zA-Z0-9_.-]+`   
Required: Yes

## Response Elements<a name="API_StopApplication_ResponseElements"></a>

If the action is successful, the service sends back an HTTP 200 response with an empty HTTP body\.

## Errors<a name="API_StopApplication_Errors"></a>

 **ResourceInUseException**   
Application is not available for this operation\.  
HTTP Status Code: 400

 **ResourceNotFoundException**   
Specified application can't be found\.  
HTTP Status Code: 400

 **UnsupportedOperationException**   
HTTP Status Code: 400

## See Also<a name="API_StopApplication_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:
+  [AWS Command Line Interface](https://docs.aws.amazon.com/goto/aws-cli/kinesisanalytics-2015-08-14/StopApplication) 
+  [AWS SDK for \.NET](https://docs.aws.amazon.com/goto/DotNetSDKV3/kinesisanalytics-2015-08-14/StopApplication) 
+  [AWS SDK for C\+\+](https://docs.aws.amazon.com/goto/SdkForCpp/kinesisanalytics-2015-08-14/StopApplication) 
+  [AWS SDK for Go](https://docs.aws.amazon.com/goto/SdkForGoV1/kinesisanalytics-2015-08-14/StopApplication) 
+  [AWS SDK for Java](https://docs.aws.amazon.com/goto/SdkForJava/kinesisanalytics-2015-08-14/StopApplication) 
+  [AWS SDK for JavaScript](https://docs.aws.amazon.com/goto/AWSJavaScriptSDK/kinesisanalytics-2015-08-14/StopApplication) 
+  [AWS SDK for PHP V3](https://docs.aws.amazon.com/goto/SdkForPHPV3/kinesisanalytics-2015-08-14/StopApplication) 
+  [AWS SDK for Python](https://docs.aws.amazon.com/goto/boto3/kinesisanalytics-2015-08-14/StopApplication) 
+  [AWS SDK for Ruby V2](https://docs.aws.amazon.com/goto/SdkForRubyV2/kinesisanalytics-2015-08-14/StopApplication) 