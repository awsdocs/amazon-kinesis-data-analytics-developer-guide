# InputDescription<a name="API_InputDescription"></a>

Describes the application input configuration\. For more information, see [Configuring Application Input](https://docs.aws.amazon.com/kinesisanalytics/latest/dev/how-it-works-input.html)\. 

## Contents<a name="API_InputDescription_Contents"></a>

 **InAppStreamNames**   <a name="analytics-Type-InputDescription-InAppStreamNames"></a>
Returns the in\-application stream names that are mapped to the stream source\.  
Type: Array of strings  
Length Constraints: Minimum length of 1\. Maximum length of 32\.  
Required: No

 **InputId**   <a name="analytics-Type-InputDescription-InputId"></a>
Input ID associated with the application input\. This is the ID that Amazon Kinesis Analytics assigns to each input configuration you add to your application\.   
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 50\.  
Pattern: `[a-zA-Z0-9_.-]+`   
Required: No

 **InputParallelism**   <a name="analytics-Type-InputDescription-InputParallelism"></a>
Describes the configured parallelism \(number of in\-application streams mapped to the streaming source\)\.  
Type: [InputParallelism](API_InputParallelism.md) object  
Required: No

 **InputProcessingConfigurationDescription**   <a name="analytics-Type-InputDescription-InputProcessingConfigurationDescription"></a>
The description of the preprocessor that executes on records in this input before the application's code is run\.  
Type: [InputProcessingConfigurationDescription](API_InputProcessingConfigurationDescription.md) object  
Required: No

 **InputSchema**   <a name="analytics-Type-InputDescription-InputSchema"></a>
Describes the format of the data in the streaming source, and how each data element maps to corresponding columns in the in\-application stream that is being created\.   
Type: [SourceSchema](API_SourceSchema.md) object  
Required: No

 **InputStartingPositionConfiguration**   <a name="analytics-Type-InputDescription-InputStartingPositionConfiguration"></a>
Point at which the application is configured to read from the input stream\.  
Type: [InputStartingPositionConfiguration](API_InputStartingPositionConfiguration.md) object  
Required: No

 **KinesisFirehoseInputDescription**   <a name="analytics-Type-InputDescription-KinesisFirehoseInputDescription"></a>
If an Amazon Kinesis Firehose delivery stream is configured as a streaming source, provides the delivery stream's ARN and an IAM role that enables Amazon Kinesis Analytics to access the stream on your behalf\.  
Type: [KinesisFirehoseInputDescription](API_KinesisFirehoseInputDescription.md) object  
Required: No

 **KinesisStreamsInputDescription**   <a name="analytics-Type-InputDescription-KinesisStreamsInputDescription"></a>
If an Amazon Kinesis stream is configured as streaming source, provides Amazon Kinesis stream's Amazon Resource Name \(ARN\) and an IAM role that enables Amazon Kinesis Analytics to access the stream on your behalf\.  
Type: [KinesisStreamsInputDescription](API_KinesisStreamsInputDescription.md) object  
Required: No

 **NamePrefix**   <a name="analytics-Type-InputDescription-NamePrefix"></a>
In\-application name prefix\.  
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 32\.  
Required: No

## See Also<a name="API_InputDescription_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:
+  [AWS SDK for C\+\+](https://docs.aws.amazon.com/goto/SdkForCpp/kinesisanalytics-2015-08-14/InputDescription) 
+  [AWS SDK for Go](https://docs.aws.amazon.com/goto/SdkForGoV1/kinesisanalytics-2015-08-14/InputDescription) 
+  [AWS SDK for Java](https://docs.aws.amazon.com/goto/SdkForJava/kinesisanalytics-2015-08-14/InputDescription) 
+  [AWS SDK for Ruby V3](https://docs.aws.amazon.com/goto/SdkForRubyV3/kinesisanalytics-2015-08-14/InputDescription) 