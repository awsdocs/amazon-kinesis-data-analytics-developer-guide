# KinesisFirehoseOutputUpdate<a name="API_KinesisFirehoseOutputUpdate"></a>

 When updating an output configuration using the [UpdateApplication](https://docs.aws.amazon.com/kinesisanalytics/latest/dev/API_UpdateApplication.html) operation, provides information about an Amazon Kinesis Firehose delivery stream configured as the destination\. 

## Contents<a name="API_KinesisFirehoseOutputUpdate_Contents"></a>

 **ResourceARNUpdate**   <a name="analytics-Type-KinesisFirehoseOutputUpdate-ResourceARNUpdate"></a>
Amazon Resource Name \(ARN\) of the Amazon Kinesis Firehose delivery stream to write to\.  
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 2048\.  
Pattern: `arn:.*`   
Required: No

 **RoleARNUpdate**   <a name="analytics-Type-KinesisFirehoseOutputUpdate-RoleARNUpdate"></a>
ARN of the IAM role that Amazon Kinesis Analytics can assume to access the stream on your behalf\. You need to grant the necessary permissions to this role\.  
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 2048\.  
Pattern: `arn:aws:iam::\d{12}:role/?[a-zA-Z_0-9+=,.@\-_/]+`   
Required: No

## See Also<a name="API_KinesisFirehoseOutputUpdate_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:
+  [AWS SDK for C\+\+](https://docs.aws.amazon.com/goto/SdkForCpp/kinesisanalytics-2015-08-14/KinesisFirehoseOutputUpdate) 
+  [AWS SDK for Go](https://docs.aws.amazon.com/goto/SdkForGoV1/kinesisanalytics-2015-08-14/KinesisFirehoseOutputUpdate) 
+  [AWS SDK for Java](https://docs.aws.amazon.com/goto/SdkForJava/kinesisanalytics-2015-08-14/KinesisFirehoseOutputUpdate) 
+  [AWS SDK for Ruby V3](https://docs.aws.amazon.com/goto/SdkForRubyV3/kinesisanalytics-2015-08-14/KinesisFirehoseOutputUpdate) 