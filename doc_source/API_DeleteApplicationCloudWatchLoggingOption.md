# DeleteApplicationCloudWatchLoggingOption<a name="API_DeleteApplicationCloudWatchLoggingOption"></a>

**Note**  
This documentation is for version 1 of the Amazon Kinesis Data Analytics API, which only supports SQL applications\. Version 2 of the API supports SQL and Java applications\. For more information about version 2, see [Amazon Kinesis Data Analytics API V2 Documentation](/kinesisanalytics/latest/apiv2/Welcome.html)\.

Deletes a CloudWatch log stream from an application\. For more information about using CloudWatch log streams with Amazon Kinesis Analytics applications, see [Working with Amazon CloudWatch Logs](https://docs.aws.amazon.com/kinesisanalytics/latest/dev/cloudwatch-logs.html)\.

## Request Syntax<a name="API_DeleteApplicationCloudWatchLoggingOption_RequestSyntax"></a>

```
{
   "[ApplicationName](#analytics-DeleteApplicationCloudWatchLoggingOption-request-ApplicationName)": "string",
   "[CloudWatchLoggingOptionId](#analytics-DeleteApplicationCloudWatchLoggingOption-request-CloudWatchLoggingOptionId)": "string",
   "[CurrentApplicationVersionId](#analytics-DeleteApplicationCloudWatchLoggingOption-request-CurrentApplicationVersionId)": number
}
```

## Request Parameters<a name="API_DeleteApplicationCloudWatchLoggingOption_RequestParameters"></a>

The request accepts the following data in JSON format\.

 ** [ApplicationName](#API_DeleteApplicationCloudWatchLoggingOption_RequestSyntax) **   <a name="analytics-DeleteApplicationCloudWatchLoggingOption-request-ApplicationName"></a>
The Kinesis Analytics application name\.  
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 128\.  
Pattern: `[a-zA-Z0-9_.-]+`   
Required: Yes

 ** [CloudWatchLoggingOptionId](#API_DeleteApplicationCloudWatchLoggingOption_RequestSyntax) **   <a name="analytics-DeleteApplicationCloudWatchLoggingOption-request-CloudWatchLoggingOptionId"></a>
The `CloudWatchLoggingOptionId` of the CloudWatch logging option to delete\. You can get the `CloudWatchLoggingOptionId` by using the [DescribeApplication](https://docs.aws.amazon.com/kinesisanalytics/latest/dev/API_DescribeApplication.html) operation\.   
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 50\.  
Pattern: `[a-zA-Z0-9_.-]+`   
Required: Yes

 ** [CurrentApplicationVersionId](#API_DeleteApplicationCloudWatchLoggingOption_RequestSyntax) **   <a name="analytics-DeleteApplicationCloudWatchLoggingOption-request-CurrentApplicationVersionId"></a>
The version ID of the Kinesis Analytics application\.  
Type: Long  
Valid Range: Minimum value of 1\. Maximum value of 999999999\.  
Required: Yes

## Response Elements<a name="API_DeleteApplicationCloudWatchLoggingOption_ResponseElements"></a>

If the action is successful, the service sends back an HTTP 200 response with an empty HTTP body\.

## Errors<a name="API_DeleteApplicationCloudWatchLoggingOption_Errors"></a>

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

## See Also<a name="API_DeleteApplicationCloudWatchLoggingOption_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:
+  [AWS Command Line Interface](https://docs.aws.amazon.com/goto/aws-cli/kinesisanalytics-2015-08-14/DeleteApplicationCloudWatchLoggingOption) 
+  [AWS SDK for \.NET](https://docs.aws.amazon.com/goto/DotNetSDKV3/kinesisanalytics-2015-08-14/DeleteApplicationCloudWatchLoggingOption) 
+  [AWS SDK for C\+\+](https://docs.aws.amazon.com/goto/SdkForCpp/kinesisanalytics-2015-08-14/DeleteApplicationCloudWatchLoggingOption) 
+  [AWS SDK for Go](https://docs.aws.amazon.com/goto/SdkForGoV1/kinesisanalytics-2015-08-14/DeleteApplicationCloudWatchLoggingOption) 
+  [AWS SDK for Java](https://docs.aws.amazon.com/goto/SdkForJava/kinesisanalytics-2015-08-14/DeleteApplicationCloudWatchLoggingOption) 
+  [AWS SDK for JavaScript](https://docs.aws.amazon.com/goto/AWSJavaScriptSDK/kinesisanalytics-2015-08-14/DeleteApplicationCloudWatchLoggingOption) 
+  [AWS SDK for PHP V3](https://docs.aws.amazon.com/goto/SdkForPHPV3/kinesisanalytics-2015-08-14/DeleteApplicationCloudWatchLoggingOption) 
+  [AWS SDK for Python](https://docs.aws.amazon.com/goto/boto3/kinesisanalytics-2015-08-14/DeleteApplicationCloudWatchLoggingOption) 
+  [AWS SDK for Ruby V3](https://docs.aws.amazon.com/goto/SdkForRubyV3/kinesisanalytics-2015-08-14/DeleteApplicationCloudWatchLoggingOption) 