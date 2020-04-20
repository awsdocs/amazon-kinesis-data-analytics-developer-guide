# CloudWatchLoggingOption<a name="API_CloudWatchLoggingOption"></a>

Provides a description of CloudWatch logging options, including the log stream Amazon Resource Name \(ARN\) and the role ARN\.

## Contents<a name="API_CloudWatchLoggingOption_Contents"></a>

 **LogStreamARN**   <a name="analytics-Type-CloudWatchLoggingOption-LogStreamARN"></a>
ARN of the CloudWatch log to receive application messages\.  
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 2048\.  
Pattern: `arn:.*`   
Required: Yes

 **RoleARN**   <a name="analytics-Type-CloudWatchLoggingOption-RoleARN"></a>
IAM ARN of the role to use to send application messages\. Note: To write application messages to CloudWatch, the IAM role that is used must have the `PutLogEvents` policy action enabled\.  
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 2048\.  
Pattern: `arn:aws:iam::\d{12}:role/?[a-zA-Z_0-9+=,.@\-_/]+`   
Required: Yes

## See Also<a name="API_CloudWatchLoggingOption_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:
+  [AWS SDK for C\+\+](https://docs.aws.amazon.com/goto/SdkForCpp/kinesisanalytics-2015-08-14/CloudWatchLoggingOption) 
+  [AWS SDK for Go](https://docs.aws.amazon.com/goto/SdkForGoV1/kinesisanalytics-2015-08-14/CloudWatchLoggingOption) 
+  [AWS SDK for Java](https://docs.aws.amazon.com/goto/SdkForJava/kinesisanalytics-2015-08-14/CloudWatchLoggingOption) 
+  [AWS SDK for Ruby V3](https://docs.aws.amazon.com/goto/SdkForRubyV3/kinesisanalytics-2015-08-14/CloudWatchLoggingOption) 