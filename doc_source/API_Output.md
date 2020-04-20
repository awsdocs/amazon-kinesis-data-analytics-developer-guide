# Output<a name="API_Output"></a>

 Describes application output configuration in which you identify an in\-application stream and a destination where you want the in\-application stream data to be written\. The destination can be an Amazon Kinesis stream or an Amazon Kinesis Firehose delivery stream\. 

For limits on how many destinations an application can write and other limitations, see [Limits](https://docs.aws.amazon.com/kinesisanalytics/latest/dev/limits.html)\. 

## Contents<a name="API_Output_Contents"></a>

 **DestinationSchema**   <a name="analytics-Type-Output-DestinationSchema"></a>
Describes the data format when records are written to the destination\. For more information, see [Configuring Application Output](https://docs.aws.amazon.com/kinesisanalytics/latest/dev/how-it-works-output.html)\.  
Type: [DestinationSchema](API_DestinationSchema.md) object  
Required: Yes

 **KinesisFirehoseOutput**   <a name="analytics-Type-Output-KinesisFirehoseOutput"></a>
Identifies an Amazon Kinesis Firehose delivery stream as the destination\.  
Type: [KinesisFirehoseOutput](API_KinesisFirehoseOutput.md) object  
Required: No

 **KinesisStreamsOutput**   <a name="analytics-Type-Output-KinesisStreamsOutput"></a>
Identifies an Amazon Kinesis stream as the destination\.  
Type: [KinesisStreamsOutput](API_KinesisStreamsOutput.md) object  
Required: No

 **LambdaOutput**   <a name="analytics-Type-Output-LambdaOutput"></a>
Identifies an AWS Lambda function as the destination\.  
Type: [LambdaOutput](API_LambdaOutput.md) object  
Required: No

 **Name**   <a name="analytics-Type-Output-Name"></a>
Name of the in\-application stream\.  
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 32\.  
Required: Yes

## See Also<a name="API_Output_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:
+  [AWS SDK for C\+\+](https://docs.aws.amazon.com/goto/SdkForCpp/kinesisanalytics-2015-08-14/Output) 
+  [AWS SDK for Go](https://docs.aws.amazon.com/goto/SdkForGoV1/kinesisanalytics-2015-08-14/Output) 
+  [AWS SDK for Java](https://docs.aws.amazon.com/goto/SdkForJava/kinesisanalytics-2015-08-14/Output) 
+  [AWS SDK for Ruby V3](https://docs.aws.amazon.com/goto/SdkForRubyV3/kinesisanalytics-2015-08-14/Output) 