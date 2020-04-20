# InputUpdate<a name="API_InputUpdate"></a>

Describes updates to a specific input configuration \(identified by the `InputId` of an application\)\. 

## Contents<a name="API_InputUpdate_Contents"></a>

 **InputId**   <a name="analytics-Type-InputUpdate-InputId"></a>
Input ID of the application input to be updated\.  
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 50\.  
Pattern: `[a-zA-Z0-9_.-]+`   
Required: Yes

 **InputParallelismUpdate**   <a name="analytics-Type-InputUpdate-InputParallelismUpdate"></a>
Describes the parallelism updates \(the number in\-application streams Amazon Kinesis Analytics creates for the specific streaming source\)\.  
Type: [InputParallelismUpdate](API_InputParallelismUpdate.md) object  
Required: No

 **InputProcessingConfigurationUpdate**   <a name="analytics-Type-InputUpdate-InputProcessingConfigurationUpdate"></a>
Describes updates for an input processing configuration\.  
Type: [InputProcessingConfigurationUpdate](API_InputProcessingConfigurationUpdate.md) object  
Required: No

 **InputSchemaUpdate**   <a name="analytics-Type-InputUpdate-InputSchemaUpdate"></a>
Describes the data format on the streaming source, and how record elements on the streaming source map to columns of the in\-application stream that is created\.  
Type: [InputSchemaUpdate](API_InputSchemaUpdate.md) object  
Required: No

 **KinesisFirehoseInputUpdate**   <a name="analytics-Type-InputUpdate-KinesisFirehoseInputUpdate"></a>
If an Amazon Kinesis Firehose delivery stream is the streaming source to be updated, provides an updated stream ARN and IAM role ARN\.  
Type: [KinesisFirehoseInputUpdate](API_KinesisFirehoseInputUpdate.md) object  
Required: No

 **KinesisStreamsInputUpdate**   <a name="analytics-Type-InputUpdate-KinesisStreamsInputUpdate"></a>
If an Amazon Kinesis stream is the streaming source to be updated, provides an updated stream Amazon Resource Name \(ARN\) and IAM role ARN\.  
Type: [KinesisStreamsInputUpdate](API_KinesisStreamsInputUpdate.md) object  
Required: No

 **NamePrefixUpdate**   <a name="analytics-Type-InputUpdate-NamePrefixUpdate"></a>
Name prefix for in\-application streams that Amazon Kinesis Analytics creates for the specific streaming source\.  
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 32\.  
Required: No

## See Also<a name="API_InputUpdate_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:
+  [AWS SDK for C\+\+](https://docs.aws.amazon.com/goto/SdkForCpp/kinesisanalytics-2015-08-14/InputUpdate) 
+  [AWS SDK for Go](https://docs.aws.amazon.com/goto/SdkForGoV1/kinesisanalytics-2015-08-14/InputUpdate) 
+  [AWS SDK for Java](https://docs.aws.amazon.com/goto/SdkForJava/kinesisanalytics-2015-08-14/InputUpdate) 
+  [AWS SDK for Ruby V3](https://docs.aws.amazon.com/goto/SdkForRubyV3/kinesisanalytics-2015-08-14/InputUpdate) 