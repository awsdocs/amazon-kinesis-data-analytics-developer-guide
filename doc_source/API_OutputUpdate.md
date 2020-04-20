# OutputUpdate<a name="API_OutputUpdate"></a>

 Describes updates to the output configuration identified by the `OutputId`\. 

## Contents<a name="API_OutputUpdate_Contents"></a>

 **DestinationSchemaUpdate**   <a name="analytics-Type-OutputUpdate-DestinationSchemaUpdate"></a>
Describes the data format when records are written to the destination\. For more information, see [Configuring Application Output](https://docs.aws.amazon.com/kinesisanalytics/latest/dev/how-it-works-output.html)\.  
Type: [DestinationSchema](API_DestinationSchema.md) object  
Required: No

 **KinesisFirehoseOutputUpdate**   <a name="analytics-Type-OutputUpdate-KinesisFirehoseOutputUpdate"></a>
Describes an Amazon Kinesis Firehose delivery stream as the destination for the output\.  
Type: [KinesisFirehoseOutputUpdate](API_KinesisFirehoseOutputUpdate.md) object  
Required: No

 **KinesisStreamsOutputUpdate**   <a name="analytics-Type-OutputUpdate-KinesisStreamsOutputUpdate"></a>
Describes an Amazon Kinesis stream as the destination for the output\.  
Type: [KinesisStreamsOutputUpdate](API_KinesisStreamsOutputUpdate.md) object  
Required: No

 **LambdaOutputUpdate**   <a name="analytics-Type-OutputUpdate-LambdaOutputUpdate"></a>
Describes an AWS Lambda function as the destination for the output\.  
Type: [LambdaOutputUpdate](API_LambdaOutputUpdate.md) object  
Required: No

 **NameUpdate**   <a name="analytics-Type-OutputUpdate-NameUpdate"></a>
If you want to specify a different in\-application stream for this output configuration, use this field to specify the new in\-application stream name\.  
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 32\.  
Required: No

 **OutputId**   <a name="analytics-Type-OutputUpdate-OutputId"></a>
Identifies the specific output configuration that you want to update\.  
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 50\.  
Pattern: `[a-zA-Z0-9_.-]+`   
Required: Yes

## See Also<a name="API_OutputUpdate_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:
+  [AWS SDK for C\+\+](https://docs.aws.amazon.com/goto/SdkForCpp/kinesisanalytics-2015-08-14/OutputUpdate) 
+  [AWS SDK for Go](https://docs.aws.amazon.com/goto/SdkForGoV1/kinesisanalytics-2015-08-14/OutputUpdate) 
+  [AWS SDK for Java](https://docs.aws.amazon.com/goto/SdkForJava/kinesisanalytics-2015-08-14/OutputUpdate) 
+  [AWS SDK for Ruby V3](https://docs.aws.amazon.com/goto/SdkForRubyV3/kinesisanalytics-2015-08-14/OutputUpdate) 