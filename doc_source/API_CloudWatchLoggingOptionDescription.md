# CloudWatchLoggingOptionDescription<a name="API_CloudWatchLoggingOptionDescription"></a>

Description of the CloudWatch logging option\.

## Contents<a name="API_CloudWatchLoggingOptionDescription_Contents"></a>

 **CloudWatchLoggingOptionId**   <a name="analytics-Type-CloudWatchLoggingOptionDescription-CloudWatchLoggingOptionId"></a>
ID of the CloudWatch logging option description\.  
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 50\.  
Pattern: `[a-zA-Z0-9_.-]+`   
Required: No

 **LogStreamARN**   <a name="analytics-Type-CloudWatchLoggingOptionDescription-LogStreamARN"></a>
ARN of the CloudWatch log to receive application messages\.  
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 2048\.  
Pattern: `arn:.*`   
Required: Yes

 **RoleARN**   <a name="analytics-Type-CloudWatchLoggingOptionDescription-RoleARN"></a>
IAM ARN of the role to use to send application messages\. Note: To write application messages to CloudWatch, the IAM role used must have the `PutLogEvents` policy action enabled\.  
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 2048\.  
Pattern: `arn:aws:iam::\d{12}:role/?[a-zA-Z_0-9+=,.@\-_/]+`   
Required: Yes

## See Also<a name="API_CloudWatchLoggingOptionDescription_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:
+  [AWS SDK for C\+\+](https://docs.aws.amazon.com/goto/SdkForCpp/kinesisanalytics-2015-08-14/CloudWatchLoggingOptionDescription) 
+  [AWS SDK for Go](https://docs.aws.amazon.com/goto/SdkForGoV1/kinesisanalytics-2015-08-14/CloudWatchLoggingOptionDescription) 
+  [AWS SDK for Java](https://docs.aws.amazon.com/goto/SdkForJava/kinesisanalytics-2015-08-14/CloudWatchLoggingOptionDescription) 
+  [AWS SDK for Ruby V3](https://docs.aws.amazon.com/goto/SdkForRubyV3/kinesisanalytics-2015-08-14/CloudWatchLoggingOptionDescription) 