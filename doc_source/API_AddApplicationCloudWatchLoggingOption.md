# AddApplicationCloudWatchLoggingOption<a name="API_AddApplicationCloudWatchLoggingOption"></a>

**Note**  
This documentation is for version 1 of the Amazon Kinesis Data Analytics API, which only supports SQL applications\. Version 2 of the API supports SQL and Java applications\. For more information about version 2, see [Amazon Kinesis Data Analytics API V2 Documentation](/kinesisanalytics/latest/apiv2/Welcome.html)\.

Adds a CloudWatch log stream to monitor application configuration errors\. For more information about using CloudWatch log streams with Amazon Kinesis Analytics applications, see [Working with Amazon CloudWatch Logs](https://docs.aws.amazon.com/kinesisanalytics/latest/dev/cloudwatch-logs.html)\.

## Request Syntax<a name="API_AddApplicationCloudWatchLoggingOption_RequestSyntax"></a>

```
{
   "[ApplicationName](#analytics-AddApplicationCloudWatchLoggingOption-request-ApplicationName)": "string",
   "[CloudWatchLoggingOption](#analytics-AddApplicationCloudWatchLoggingOption-request-CloudWatchLoggingOption)": { 
      "[LogStreamARN](API_CloudWatchLoggingOption.md#analytics-Type-CloudWatchLoggingOption-LogStreamARN)": "string",
      "[RoleARN](API_CloudWatchLoggingOption.md#analytics-Type-CloudWatchLoggingOption-RoleARN)": "string"
   },
   "[CurrentApplicationVersionId](#analytics-AddApplicationCloudWatchLoggingOption-request-CurrentApplicationVersionId)": number
}
```

## Request Parameters<a name="API_AddApplicationCloudWatchLoggingOption_RequestParameters"></a>

The request accepts the following data in JSON format\.

 ** [ApplicationName](#API_AddApplicationCloudWatchLoggingOption_RequestSyntax) **   <a name="analytics-AddApplicationCloudWatchLoggingOption-request-ApplicationName"></a>
The Kinesis Analytics application name\.  
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 128\.  
Pattern: `[a-zA-Z0-9_.-]+`   
Required: Yes

 ** [CloudWatchLoggingOption](#API_AddApplicationCloudWatchLoggingOption_RequestSyntax) **   <a name="analytics-AddApplicationCloudWatchLoggingOption-request-CloudWatchLoggingOption"></a>
Provides the CloudWatch log stream Amazon Resource Name \(ARN\) and the IAM role ARN\. Note: To write application messages to CloudWatch, the IAM role that is used must have the `PutLogEvents` policy action enabled\.  
Type: [CloudWatchLoggingOption](API_CloudWatchLoggingOption.md) object  
Required: Yes

 ** [CurrentApplicationVersionId](#API_AddApplicationCloudWatchLoggingOption_RequestSyntax) **   <a name="analytics-AddApplicationCloudWatchLoggingOption-request-CurrentApplicationVersionId"></a>
The version ID of the Kinesis Analytics application\.  
Type: Long  
Valid Range: Minimum value of 1\. Maximum value of 999999999\.  
Required: Yes

## Response Elements<a name="API_AddApplicationCloudWatchLoggingOption_ResponseElements"></a>

If the action is successful, the service sends back an HTTP 200 response with an empty HTTP body\.

## Errors<a name="API_AddApplicationCloudWatchLoggingOption_Errors"></a>

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

## See Also<a name="API_AddApplicationCloudWatchLoggingOption_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:
+  [AWS Command Line Interface](https://docs.aws.amazon.com/goto/aws-cli/kinesisanalytics-2015-08-14/AddApplicationCloudWatchLoggingOption) 
+  [AWS SDK for \.NET](https://docs.aws.amazon.com/goto/DotNetSDKV3/kinesisanalytics-2015-08-14/AddApplicationCloudWatchLoggingOption) 
+  [AWS SDK for C\+\+](https://docs.aws.amazon.com/goto/SdkForCpp/kinesisanalytics-2015-08-14/AddApplicationCloudWatchLoggingOption) 
+  [AWS SDK for Go](https://docs.aws.amazon.com/goto/SdkForGoV1/kinesisanalytics-2015-08-14/AddApplicationCloudWatchLoggingOption) 
+  [AWS SDK for Java](https://docs.aws.amazon.com/goto/SdkForJava/kinesisanalytics-2015-08-14/AddApplicationCloudWatchLoggingOption) 
+  [AWS SDK for JavaScript](https://docs.aws.amazon.com/goto/AWSJavaScriptSDK/kinesisanalytics-2015-08-14/AddApplicationCloudWatchLoggingOption) 
+  [AWS SDK for PHP V3](https://docs.aws.amazon.com/goto/SdkForPHPV3/kinesisanalytics-2015-08-14/AddApplicationCloudWatchLoggingOption) 
+  [AWS SDK for Python](https://docs.aws.amazon.com/goto/boto3/kinesisanalytics-2015-08-14/AddApplicationCloudWatchLoggingOption) 
+  [AWS SDK for Ruby V3](https://docs.aws.amazon.com/goto/SdkForRubyV3/kinesisanalytics-2015-08-14/AddApplicationCloudWatchLoggingOption) 