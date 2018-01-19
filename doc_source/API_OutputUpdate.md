# OutputUpdate<a name="API_OutputUpdate"></a>

 Describes updates to the output configuration identified by the `OutputId`\. 

## Contents<a name="API_OutputUpdate_Contents"></a>

 **DestinationSchemaUpdate**   
Describes the data format when records are written to the destination\. For more information, see [Configuring Application Output](http://docs.aws.amazon.com/kinesisanalytics/latest/dev/how-it-works-output.html)\.  
Type: [DestinationSchema](API_DestinationSchema.md) object  
Required: No

 **KinesisFirehoseOutputUpdate**   
Describes an Amazon Kinesis Firehose delivery stream as the destination for the output\.  
Type: [KinesisFirehoseOutputUpdate](API_KinesisFirehoseOutputUpdate.md) object  
Required: No

 **KinesisStreamsOutputUpdate**   
Describes an Amazon Kinesis stream as the destination for the output\.  
Type: [KinesisStreamsOutputUpdate](API_KinesisStreamsOutputUpdate.md) object  
Required: No

 **LambdaOutputUpdate**   
Describes an AWS Lambda function as the destination for the output\.  
Type: [LambdaOutputUpdate](API_LambdaOutputUpdate.md) object  
Required: No

 **NameUpdate**   
If you want to specify a different in\-application stream for this output configuration, use this field to specify the new in\-application stream name\.  
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 32\.  
Pattern: `[a-zA-Z][a-zA-Z0-9_]+`   
Required: No

 **OutputId**   
Identifies the specific output configuration that you want to update\.  
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 50\.  
Pattern: `[a-zA-Z0-9_.-]+`   
Required: Yes

## See Also<a name="API_OutputUpdate_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:

+  [AWS SDK for C\+\+](http://docs.aws.amazon.com/goto/SdkForCpp/kinesisanalytics-2015-08-14/OutputUpdate) 

+  [AWS SDK for Go](http://docs.aws.amazon.com/goto/SdkForGoV1/kinesisanalytics-2015-08-14/OutputUpdate) 

+  [AWS SDK for Java](http://docs.aws.amazon.com/goto/SdkForJava/kinesisanalytics-2015-08-14/OutputUpdate) 

+  [AWS SDK for Ruby V2](http://docs.aws.amazon.com/goto/SdkForRubyV2/kinesisanalytics-2015-08-14/OutputUpdate) 