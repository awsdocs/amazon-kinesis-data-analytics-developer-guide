# KinesisStreamsInput<a name="API_KinesisStreamsInput"></a>

 Identifies an Amazon Kinesis stream as the streaming source\. You provide the stream's Amazon Resource Name \(ARN\) and an IAM role ARN that enables Amazon Kinesis Analytics to access the stream on your behalf\.

## Contents<a name="API_KinesisStreamsInput_Contents"></a>

 **ResourceARN**   <a name="analytics-Type-KinesisStreamsInput-ResourceARN"></a>
ARN of the input Amazon Kinesis stream to read\.  
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 2048\.  
Pattern: `arn:.*`   
Required: Yes

 **RoleARN**   <a name="analytics-Type-KinesisStreamsInput-RoleARN"></a>
ARN of the IAM role that Amazon Kinesis Analytics can assume to access the stream on your behalf\. You need to grant the necessary permissions to this role\.  
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 2048\.  
Pattern: `arn:aws:iam::\d{12}:role/?[a-zA-Z_0-9+=,.@\-_/]+`   
Required: Yes

## See Also<a name="API_KinesisStreamsInput_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:
+  [AWS SDK for C\+\+](https://docs.aws.amazon.com/goto/SdkForCpp/kinesisanalytics-2015-08-14/KinesisStreamsInput) 
+  [AWS SDK for Go](https://docs.aws.amazon.com/goto/SdkForGoV1/kinesisanalytics-2015-08-14/KinesisStreamsInput) 
+  [AWS SDK for Java](https://docs.aws.amazon.com/goto/SdkForJava/kinesisanalytics-2015-08-14/KinesisStreamsInput) 
+  [AWS SDK for Ruby V3](https://docs.aws.amazon.com/goto/SdkForRubyV3/kinesisanalytics-2015-08-14/KinesisStreamsInput) 