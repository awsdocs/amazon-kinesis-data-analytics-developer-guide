# KinesisStreamsInputUpdate<a name="API_KinesisStreamsInputUpdate"></a>

When updating application input configuration, provides information about an Amazon Kinesis stream as the streaming source\.

## Contents<a name="API_KinesisStreamsInputUpdate_Contents"></a>

 **ResourceARNUpdate**   <a name="analytics-Type-KinesisStreamsInputUpdate-ResourceARNUpdate"></a>
Amazon Resource Name \(ARN\) of the input Amazon Kinesis stream to read\.  
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 2048\.  
Pattern: `arn:.*`   
Required: No

 **RoleARNUpdate**   <a name="analytics-Type-KinesisStreamsInputUpdate-RoleARNUpdate"></a>
ARN of the IAM role that Amazon Kinesis Analytics can assume to access the stream on your behalf\. You need to grant the necessary permissions to this role\.  
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 2048\.  
Pattern: `arn:aws:iam::\d{12}:role/?[a-zA-Z_0-9+=,.@\-_/]+`   
Required: No

## See Also<a name="API_KinesisStreamsInputUpdate_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:
+  [AWS SDK for C\+\+](https://docs.aws.amazon.com/goto/SdkForCpp/kinesisanalytics-2015-08-14/KinesisStreamsInputUpdate) 
+  [AWS SDK for Go](https://docs.aws.amazon.com/goto/SdkForGoV1/kinesisanalytics-2015-08-14/KinesisStreamsInputUpdate) 
+  [AWS SDK for Java](https://docs.aws.amazon.com/goto/SdkForJava/kinesisanalytics-2015-08-14/KinesisStreamsInputUpdate) 
+  [AWS SDK for Ruby V3](https://docs.aws.amazon.com/goto/SdkForRubyV3/kinesisanalytics-2015-08-14/KinesisStreamsInputUpdate) 