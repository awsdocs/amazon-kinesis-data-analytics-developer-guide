# DeleteApplicationReferenceDataSource<a name="API_DeleteApplicationReferenceDataSource"></a>

Deletes a reference data source configuration from the specified application configuration\.

If the application is running, Amazon Kinesis Analytics immediately removes the in\-application table that you created using the [AddApplicationReferenceDataSource](API_AddApplicationReferenceDataSource.md) operation\. 

This operation requires permissions to perform the `kinesisanalytics.DeleteApplicationReferenceDataSource` action\.

## Request Syntax<a name="API_DeleteApplicationReferenceDataSource_RequestSyntax"></a>

```
{
   "[ApplicationName](#analytics-DeleteApplicationReferenceDataSource-request-ApplicationName)": "string",
   "[CurrentApplicationVersionId](#analytics-DeleteApplicationReferenceDataSource-request-CurrentApplicationVersionId)": number,
   "[ReferenceId](#analytics-DeleteApplicationReferenceDataSource-request-ReferenceId)": "string"
}
```

## Request Parameters<a name="API_DeleteApplicationReferenceDataSource_RequestParameters"></a>

The request accepts the following data in JSON format\.

 ** [ApplicationName](#API_DeleteApplicationReferenceDataSource_RequestSyntax) **   <a name="analytics-DeleteApplicationReferenceDataSource-request-ApplicationName"></a>
Name of an existing application\.  
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 128\.  
Pattern: `[a-zA-Z0-9_.-]+`   
Required: Yes

 ** [CurrentApplicationVersionId](#API_DeleteApplicationReferenceDataSource_RequestSyntax) **   <a name="analytics-DeleteApplicationReferenceDataSource-request-CurrentApplicationVersionId"></a>
Version of the application\. You can use the [DescribeApplication](API_DescribeApplication.md) operation to get the current application version\. If the version specified is not the current version, the `ConcurrentModificationException` is returned\.  
Type: Long  
Valid Range: Minimum value of 1\. Maximum value of 999999999\.  
Required: Yes

 ** [ReferenceId](#API_DeleteApplicationReferenceDataSource_RequestSyntax) **   <a name="analytics-DeleteApplicationReferenceDataSource-request-ReferenceId"></a>
ID of the reference data source\. When you add a reference data source to your application using the [AddApplicationReferenceDataSource](API_AddApplicationReferenceDataSource.md), Amazon Kinesis Analytics assigns an ID\. You can use the [DescribeApplication](API_DescribeApplication.md) operation to get the reference ID\.   
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 50\.  
Pattern: `[a-zA-Z0-9_.-]+`   
Required: Yes

## Response Elements<a name="API_DeleteApplicationReferenceDataSource_ResponseElements"></a>

If the action is successful, the service sends back an HTTP 200 response with an empty HTTP body\.

## Errors<a name="API_DeleteApplicationReferenceDataSource_Errors"></a>

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

## See Also<a name="API_DeleteApplicationReferenceDataSource_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:
+  [AWS Command Line Interface](https://docs.aws.amazon.com/goto/aws-cli/kinesisanalytics-2015-08-14/DeleteApplicationReferenceDataSource) 
+  [AWS SDK for \.NET](https://docs.aws.amazon.com/goto/DotNetSDKV3/kinesisanalytics-2015-08-14/DeleteApplicationReferenceDataSource) 
+  [AWS SDK for C\+\+](https://docs.aws.amazon.com/goto/SdkForCpp/kinesisanalytics-2015-08-14/DeleteApplicationReferenceDataSource) 
+  [AWS SDK for Go](https://docs.aws.amazon.com/goto/SdkForGoV1/kinesisanalytics-2015-08-14/DeleteApplicationReferenceDataSource) 
+  [AWS SDK for Java](https://docs.aws.amazon.com/goto/SdkForJava/kinesisanalytics-2015-08-14/DeleteApplicationReferenceDataSource) 
+  [AWS SDK for JavaScript](https://docs.aws.amazon.com/goto/AWSJavaScriptSDK/kinesisanalytics-2015-08-14/DeleteApplicationReferenceDataSource) 
+  [AWS SDK for PHP V3](https://docs.aws.amazon.com/goto/SdkForPHPV3/kinesisanalytics-2015-08-14/DeleteApplicationReferenceDataSource) 
+  [AWS SDK for Python](https://docs.aws.amazon.com/goto/boto3/kinesisanalytics-2015-08-14/DeleteApplicationReferenceDataSource) 
+  [AWS SDK for Ruby V2](https://docs.aws.amazon.com/goto/SdkForRubyV2/kinesisanalytics-2015-08-14/DeleteApplicationReferenceDataSource) 