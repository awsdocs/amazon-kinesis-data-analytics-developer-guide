# KinesisFirehoseOutputDescription<a name="API_KinesisFirehoseOutputDescription"></a>

 For an application output, describes the Amazon Kinesis Firehose delivery stream configured as its destination\. 

## Contents<a name="API_KinesisFirehoseOutputDescription_Contents"></a>

 ** ResourceARN **   <a name="analytics-Type-KinesisFirehoseOutputDescription-ResourceARN"></a>
Amazon Resource Name \(ARN\) of the Amazon Kinesis Firehose delivery stream\.  
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 2048\.  
Pattern: `arn:.*`   
Required: No

 ** RoleARN **   <a name="analytics-Type-KinesisFirehoseOutputDescription-RoleARN"></a>
ARN of the IAM role that Amazon Kinesis Analytics can assume to access the stream\.  
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 2048\.  
Pattern: `arn:.*`   
Required: No

## See Also<a name="API_KinesisFirehoseOutputDescription_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:
+  [AWS SDK for C\+\+](https://docs.aws.amazon.com/goto/SdkForCpp/kinesisanalytics-2015-08-14/KinesisFirehoseOutputDescription) 
+  [AWS SDK for Go](https://docs.aws.amazon.com/goto/SdkForGoV1/kinesisanalytics-2015-08-14/KinesisFirehoseOutputDescription) 
+  [AWS SDK for Java V2](https://docs.aws.amazon.com/goto/SdkForJavaV2/kinesisanalytics-2015-08-14/KinesisFirehoseOutputDescription) 
+  [AWS SDK for Ruby V3](https://docs.aws.amazon.com/goto/SdkForRubyV3/kinesisanalytics-2015-08-14/KinesisFirehoseOutputDescription) 