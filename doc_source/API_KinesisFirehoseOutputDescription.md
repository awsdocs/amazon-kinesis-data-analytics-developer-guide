# KinesisFirehoseOutputDescription<a name="API_KinesisFirehoseOutputDescription"></a>

 For an application output, describes the Amazon Kinesis Firehose delivery stream configured as its destination\. 

## Contents<a name="API_KinesisFirehoseOutputDescription_Contents"></a>

 **ResourceARN**   <a name="analytics-Type-KinesisFirehoseOutputDescription-ResourceARN"></a>
Amazon Resource Name \(ARN\) of the Amazon Kinesis Firehose delivery stream\.  
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 2048\.  
Pattern: `arn:.*`   
Required: No

 **RoleARN**   <a name="analytics-Type-KinesisFirehoseOutputDescription-RoleARN"></a>
ARN of the IAM role that Amazon Kinesis Analytics can assume to access the stream\.  
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 2048\.  
Pattern: `arn:aws:iam::\d{12}:role/?[a-zA-Z_0-9+=,.@\-_/]+`   
Required: No

## See Also<a name="API_KinesisFirehoseOutputDescription_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:

+  [AWS SDK for C\+\+](http://docs.aws.amazon.com/goto/SdkForCpp/kinesisanalytics-2015-08-14/KinesisFirehoseOutputDescription) 

+  [AWS SDK for Go](http://docs.aws.amazon.com/goto/SdkForGoV1/kinesisanalytics-2015-08-14/KinesisFirehoseOutputDescription) 

+  [AWS SDK for Java](http://docs.aws.amazon.com/goto/SdkForJava/kinesisanalytics-2015-08-14/KinesisFirehoseOutputDescription) 

+  [AWS SDK for Ruby V2](http://docs.aws.amazon.com/goto/SdkForRubyV2/kinesisanalytics-2015-08-14/KinesisFirehoseOutputDescription) 