# KinesisFirehoseOutput<a name="API_KinesisFirehoseOutput"></a>

When configuring application output, identifies an Amazon Kinesis Firehose delivery stream as the destination\. You provide the stream Amazon Resource Name \(ARN\) and an IAM role that enables Amazon Kinesis Analytics to write to the stream on your behalf\.

## Contents<a name="API_KinesisFirehoseOutput_Contents"></a>

 **ResourceARN**   <a name="analytics-Type-KinesisFirehoseOutput-ResourceARN"></a>
ARN of the destination Amazon Kinesis Firehose delivery stream to write to\.  
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 2048\.  
Pattern: `arn:.*`   
Required: Yes

 **RoleARN**   <a name="analytics-Type-KinesisFirehoseOutput-RoleARN"></a>
ARN of the IAM role that Amazon Kinesis Analytics can assume to write to the destination stream on your behalf\. You need to grant the necessary permissions to this role\.  
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 2048\.  
Pattern: `arn:aws:iam::\d{12}:role/?[a-zA-Z_0-9+=,.@\-_/]+`   
Required: Yes

## See Also<a name="API_KinesisFirehoseOutput_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:
+  [AWS SDK for C\+\+](https://docs.aws.amazon.com/goto/SdkForCpp/kinesisanalytics-2015-08-14/KinesisFirehoseOutput) 
+  [AWS SDK for Go](https://docs.aws.amazon.com/goto/SdkForGoV1/kinesisanalytics-2015-08-14/KinesisFirehoseOutput) 
+  [AWS SDK for Java](https://docs.aws.amazon.com/goto/SdkForJava/kinesisanalytics-2015-08-14/KinesisFirehoseOutput) 
+  [AWS SDK for Ruby V3](https://docs.aws.amazon.com/goto/SdkForRubyV3/kinesisanalytics-2015-08-14/KinesisFirehoseOutput) 