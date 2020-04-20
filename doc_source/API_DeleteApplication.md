# DeleteApplication<a name="API_DeleteApplication"></a>

**Note**  
This documentation is for version 1 of the Amazon Kinesis Data Analytics API, which only supports SQL applications\. Version 2 of the API supports SQL and Java applications\. For more information about version 2, see [Amazon Kinesis Data Analytics API V2 Documentation](/kinesisanalytics/latest/apiv2/Welcome.html)\.

Deletes the specified application\. Amazon Kinesis Analytics halts application execution and deletes the application, including any application artifacts \(such as in\-application streams, reference table, and application code\)\.

This operation requires permissions to perform the `kinesisanalytics:DeleteApplication` action\.

## Request Syntax<a name="API_DeleteApplication_RequestSyntax"></a>

```
{
   "[ApplicationName](#analytics-DeleteApplication-request-ApplicationName)": "string",
   "[CreateTimestamp](#analytics-DeleteApplication-request-CreateTimestamp)": number
}
```

## Request Parameters<a name="API_DeleteApplication_RequestParameters"></a>

The request accepts the following data in JSON format\.

 ** [ApplicationName](#API_DeleteApplication_RequestSyntax) **   <a name="analytics-DeleteApplication-request-ApplicationName"></a>
Name of the Amazon Kinesis Analytics application to delete\.  
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 128\.  
Pattern: `[a-zA-Z0-9_.-]+`   
Required: Yes

 ** [CreateTimestamp](#API_DeleteApplication_RequestSyntax) **   <a name="analytics-DeleteApplication-request-CreateTimestamp"></a>
 You can use the `DescribeApplication` operation to get this value\.   
Type: Timestamp  
Required: Yes

## Response Elements<a name="API_DeleteApplication_ResponseElements"></a>

If the action is successful, the service sends back an HTTP 200 response with an empty HTTP body\.

## Errors<a name="API_DeleteApplication_Errors"></a>

 **ConcurrentModificationException**   
Exception thrown as a result of concurrent modification to an application\. For example, two individuals attempting to edit the same application at the same time\.  
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

## See Also<a name="API_DeleteApplication_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:
+  [AWS Command Line Interface](https://docs.aws.amazon.com/goto/aws-cli/kinesisanalytics-2015-08-14/DeleteApplication) 
+  [AWS SDK for \.NET](https://docs.aws.amazon.com/goto/DotNetSDKV3/kinesisanalytics-2015-08-14/DeleteApplication) 
+  [AWS SDK for C\+\+](https://docs.aws.amazon.com/goto/SdkForCpp/kinesisanalytics-2015-08-14/DeleteApplication) 
+  [AWS SDK for Go](https://docs.aws.amazon.com/goto/SdkForGoV1/kinesisanalytics-2015-08-14/DeleteApplication) 
+  [AWS SDK for Java](https://docs.aws.amazon.com/goto/SdkForJava/kinesisanalytics-2015-08-14/DeleteApplication) 
+  [AWS SDK for JavaScript](https://docs.aws.amazon.com/goto/AWSJavaScriptSDK/kinesisanalytics-2015-08-14/DeleteApplication) 
+  [AWS SDK for PHP V3](https://docs.aws.amazon.com/goto/SdkForPHPV3/kinesisanalytics-2015-08-14/DeleteApplication) 
+  [AWS SDK for Python](https://docs.aws.amazon.com/goto/boto3/kinesisanalytics-2015-08-14/DeleteApplication) 
+  [AWS SDK for Ruby V3](https://docs.aws.amazon.com/goto/SdkForRubyV3/kinesisanalytics-2015-08-14/DeleteApplication) 