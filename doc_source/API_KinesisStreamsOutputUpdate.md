# KinesisStreamsOutputUpdate<a name="API_KinesisStreamsOutputUpdate"></a>

 When updating an output configuration using the [UpdateApplication](API_UpdateApplication.md) operation, provides information about an Amazon Kinesis stream configured as the destination\. 

## Contents<a name="API_KinesisStreamsOutputUpdate_Contents"></a>

 **ResourceARNUpdate**   <a name="analytics-Type-KinesisStreamsOutputUpdate-ResourceARNUpdate"></a>
Amazon Resource Name \(ARN\) of the Amazon Kinesis stream where you want to write the output\.  
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 2048\.  
Pattern: `arn:.*`   
Required: No

 **RoleARNUpdate**   <a name="analytics-Type-KinesisStreamsOutputUpdate-RoleARNUpdate"></a>
ARN of the IAM role that Amazon Kinesis Analytics can assume to access the stream on your behalf\. You need to grant the necessary permissions to this role\.  
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 2048\.  
Pattern: `arn:aws:iam::\d{12}:role/?[a-zA-Z_0-9+=,.@\-_/]+`   
Required: No

## See Also<a name="API_KinesisStreamsOutputUpdate_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:
+  [AWS SDK for C\+\+](http://docs.aws.amazon.com/goto/SdkForCpp/kinesisanalytics-2015-08-14/KinesisStreamsOutputUpdate) 
+  [AWS SDK for Go](http://docs.aws.amazon.com/goto/SdkForGoV1/kinesisanalytics-2015-08-14/KinesisStreamsOutputUpdate) 
+  [AWS SDK for Java](http://docs.aws.amazon.com/goto/SdkForJava/kinesisanalytics-2015-08-14/KinesisStreamsOutputUpdate) 
+  [AWS SDK for Ruby V2](http://docs.aws.amazon.com/goto/SdkForRubyV2/kinesisanalytics-2015-08-14/KinesisStreamsOutputUpdate) 