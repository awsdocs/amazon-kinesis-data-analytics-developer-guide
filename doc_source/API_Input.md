# Input<a name="API_Input"></a>

When you configure the application input, you specify the streaming source, the in\-application stream name that is created, and the mapping between the two\. For more information, see [Configuring Application Input](http://docs.aws.amazon.com/kinesisanalytics/latest/dev/how-it-works-input.html)\. 

## Contents<a name="API_Input_Contents"></a>

 **InputParallelism**   
Describes the number of in\-application streams to create\.   
Data from your source is routed to these in\-application input streams\.  
 \(see [Configuring Application Input](http://docs.aws.amazon.com/kinesisanalytics/latest/dev/how-it-works-input.html)\.  
Type: [InputParallelism](API_InputParallelism.md) object  
Required: No

 **InputProcessingConfiguration**   
The [InputProcessingConfiguration](API_InputProcessingConfiguration.md) for the input\. An input processor transforms records as they are received from the stream, before the application's SQL code executes\. Currently, the only input processing configuration available is [InputLambdaProcessor](API_InputLambdaProcessor.md)\.  
Type: [InputProcessingConfiguration](API_InputProcessingConfiguration.md) object  
Required: No

 **InputSchema**   
Describes the format of the data in the streaming source, and how each data element maps to corresponding columns in the in\-application stream that is being created\.  
Also used to describe the format of the reference data source\.  
Type: [SourceSchema](API_SourceSchema.md) object  
Required: Yes

 **KinesisFirehoseInput**   
If the streaming source is an Amazon Kinesis Firehose delivery stream, identifies the delivery stream's ARN and an IAM role that enables Amazon Kinesis Analytics to access the stream on your behalf\.  
Note: Either `KinesisStreamsInput` or `KinesisFirehoseInput` is required\.  
Type: [KinesisFirehoseInput](API_KinesisFirehoseInput.md) object  
Required: No

 **KinesisStreamsInput**   
If the streaming source is an Amazon Kinesis stream, identifies the stream's Amazon Resource Name \(ARN\) and an IAM role that enables Amazon Kinesis Analytics to access the stream on your behalf\.  
Note: Either `KinesisStreamsInput` or `KinesisFirehoseInput` is required\.  
Type: [KinesisStreamsInput](API_KinesisStreamsInput.md) object  
Required: No

 **NamePrefix**   
Name prefix to use when creating an in\-application stream\. Suppose that you specify a prefix "MyInApplicationStream\." Amazon Kinesis Analytics then creates one or more \(as per the `InputParallelism` count you specified\) in\-application streams with names "MyInApplicationStream\_001," "MyInApplicationStream\_002," and so on\.   
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 32\.  
Pattern: `[a-zA-Z][a-zA-Z0-9_]+`   
Required: Yes

## See Also<a name="API_Input_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:

+  [AWS SDK for C\+\+](http://docs.aws.amazon.com/goto/SdkForCpp/kinesisanalytics-2015-08-14/Input) 

+  [AWS SDK for Go](http://docs.aws.amazon.com/goto/SdkForGoV1/kinesisanalytics-2015-08-14/Input) 

+  [AWS SDK for Java](http://docs.aws.amazon.com/goto/SdkForJava/kinesisanalytics-2015-08-14/Input) 

+  [AWS SDK for Ruby V2](http://docs.aws.amazon.com/goto/SdkForRubyV2/kinesisanalytics-2015-08-14/Input) 