# KinesisFirehoseInputUpdate<a name="API_KinesisFirehoseInputUpdate"></a>

When updating application input configuration, provides information about an Amazon Kinesis Firehose delivery stream as the streaming source\.

## Contents<a name="API_KinesisFirehoseInputUpdate_Contents"></a>

 **ResourceARNUpdate**   <a name="analytics-Type-KinesisFirehoseInputUpdate-ResourceARNUpdate"></a>
Amazon Resource Name \(ARN\) of the input Amazon Kinesis Firehose delivery stream to read\.  
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 2048\.  
Pattern: `arn:.*`   
Required: No

 **RoleARNUpdate**   <a name="analytics-Type-KinesisFirehoseInputUpdate-RoleARNUpdate"></a>
ARN of the IAM role that Amazon Kinesis Analytics can assume to access the stream on your behalf\. You need to grant the necessary permissions to this role\.  
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 2048\.  
Pattern: `arn:aws:iam::\d{12}:role/?[a-zA-Z_0-9+=,.@\-_/]+`   
Required: No

## See Also<a name="API_KinesisFirehoseInputUpdate_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:
+  [AWS SDK for C\+\+](https://docs.aws.amazon.com/goto/SdkForCpp/kinesisanalytics-2015-08-14/KinesisFirehoseInputUpdate) 
+  [AWS SDK for Go](https://docs.aws.amazon.com/goto/SdkForGoV1/kinesisanalytics-2015-08-14/KinesisFirehoseInputUpdate) 
+  [AWS SDK for Java](https://docs.aws.amazon.com/goto/SdkForJava/kinesisanalytics-2015-08-14/KinesisFirehoseInputUpdate) 
+  [AWS SDK for Ruby V3](https://docs.aws.amazon.com/goto/SdkForRubyV3/kinesisanalytics-2015-08-14/KinesisFirehoseInputUpdate) 