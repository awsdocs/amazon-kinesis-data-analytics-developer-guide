# Output<a name="API_Output"></a>

 Describes application output configuration in which you identify an in\-application stream and a destination where you want the in\-application stream data to be written\. The destination can be an Amazon Kinesis stream or an Amazon Kinesis Firehose delivery stream\. 



For limits on how many destinations an application can write and other limitations, see [Limits](http://docs.aws.amazon.com/kinesisanalytics/latest/dev/limits.html)\. 

## Contents<a name="API_Output_Contents"></a>

 **DestinationSchema**   
Describes the data format when records are written to the destination\. For more information, see [Configuring Application Output](http://docs.aws.amazon.com/kinesisanalytics/latest/dev/how-it-works-output.html)\.  
Type: [DestinationSchema](API_DestinationSchema.md) object  
Required: Yes

 **KinesisFirehoseOutput**   
Identifies an Amazon Kinesis Firehose delivery stream as the destination\.  
Type: [KinesisFirehoseOutput](API_KinesisFirehoseOutput.md) object  
Required: No

 **KinesisStreamsOutput**   
Identifies an Amazon Kinesis stream as the destination\.  
Type: [KinesisStreamsOutput](API_KinesisStreamsOutput.md) object  
Required: No

 **LambdaOutput**   
Identifies an AWS Lambda function as the destination\.  
Type: [LambdaOutput](API_LambdaOutput.md) object  
Required: No

 **Name**   
Name of the in\-application stream\.  
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 32\.  
Pattern: `[a-zA-Z][a-zA-Z0-9_]+`   
Required: Yes

## See Also<a name="API_Output_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:

+  [AWS SDK for C\+\+](http://docs.aws.amazon.com/goto/SdkForCpp/kinesisanalytics-2015-08-14/Output) 

+  [AWS SDK for Go](http://docs.aws.amazon.com/goto/SdkForGoV1/kinesisanalytics-2015-08-14/Output) 

+  [AWS SDK for Java](http://docs.aws.amazon.com/goto/SdkForJava/kinesisanalytics-2015-08-14/Output) 

+  [AWS SDK for Ruby V2](http://docs.aws.amazon.com/goto/SdkForRubyV2/kinesisanalytics-2015-08-14/Output) 