# KinesisStreamsOutput<a name="API_KinesisStreamsOutput"></a>

When configuring application output, identifies an Amazon Kinesis stream as the destination\. You provide the stream Amazon Resource Name \(ARN\) and also an IAM role ARN that Amazon Kinesis Analytics can use to write to the stream on your behalf\.

## Contents<a name="API_KinesisStreamsOutput_Contents"></a>

 **ResourceARN**   
ARN of the destination Amazon Kinesis stream to write to\.  
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 2048\.  
Pattern: `arn:.*`   
Required: Yes

 **RoleARN**   
ARN of the IAM role that Amazon Kinesis Analytics can assume to write to the destination stream on your behalf\. You need to grant the necessary permissions to this role\.  
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 2048\.  
Pattern: `arn:aws:iam::\d{12}:role/?[a-zA-Z_0-9+=,.@\-_/]+`   
Required: Yes

## See Also<a name="API_KinesisStreamsOutput_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:

+  [AWS SDK for C\+\+](http://docs.aws.amazon.com/goto/SdkForCpp/kinesisanalytics-2015-08-14/KinesisStreamsOutput) 

+  [AWS SDK for Go](http://docs.aws.amazon.com/goto/SdkForGoV1/kinesisanalytics-2015-08-14/KinesisStreamsOutput) 

+  [AWS SDK for Java](http://docs.aws.amazon.com/goto/SdkForJava/kinesisanalytics-2015-08-14/KinesisStreamsOutput) 

+  [AWS SDK for Ruby V2](http://docs.aws.amazon.com/goto/SdkForRubyV2/kinesisanalytics-2015-08-14/KinesisStreamsOutput) 