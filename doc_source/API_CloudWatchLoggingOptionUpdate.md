# CloudWatchLoggingOptionUpdate<a name="API_CloudWatchLoggingOptionUpdate"></a>

Describes CloudWatch logging option updates\.

## Contents<a name="API_CloudWatchLoggingOptionUpdate_Contents"></a>

 **CloudWatchLoggingOptionId**   <a name="analytics-Type-CloudWatchLoggingOptionUpdate-CloudWatchLoggingOptionId"></a>
ID of the CloudWatch logging option to update  
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 50\.  
Pattern: `[a-zA-Z0-9_.-]+`   
Required: Yes

 **LogStreamARNUpdate**   <a name="analytics-Type-CloudWatchLoggingOptionUpdate-LogStreamARNUpdate"></a>
ARN of the CloudWatch log to receive application messages\.  
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 2048\.  
Pattern: `arn:.*`   
Required: No

 **RoleARNUpdate**   <a name="analytics-Type-CloudWatchLoggingOptionUpdate-RoleARNUpdate"></a>
IAM ARN of the role to use to send application messages\. Note: To write application messages to CloudWatch, the IAM role used must have the `PutLogEvents` policy action enabled\.  
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 2048\.  
Pattern: `arn:aws:iam::\d{12}:role/?[a-zA-Z_0-9+=,.@\-_/]+`   
Required: No

## See Also<a name="API_CloudWatchLoggingOptionUpdate_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:
+  [AWS SDK for C\+\+](https://docs.aws.amazon.com/goto/SdkForCpp/kinesisanalytics-2015-08-14/CloudWatchLoggingOptionUpdate) 
+  [AWS SDK for Go](https://docs.aws.amazon.com/goto/SdkForGoV1/kinesisanalytics-2015-08-14/CloudWatchLoggingOptionUpdate) 
+  [AWS SDK for Java](https://docs.aws.amazon.com/goto/SdkForJava/kinesisanalytics-2015-08-14/CloudWatchLoggingOptionUpdate) 
+  [AWS SDK for Ruby V3](https://docs.aws.amazon.com/goto/SdkForRubyV3/kinesisanalytics-2015-08-14/CloudWatchLoggingOptionUpdate) 