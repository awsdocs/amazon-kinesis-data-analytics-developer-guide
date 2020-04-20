# OutputDescription<a name="API_OutputDescription"></a>

Describes the application output configuration, which includes the in\-application stream name and the destination where the stream data is written\. The destination can be an Amazon Kinesis stream or an Amazon Kinesis Firehose delivery stream\. 

## Contents<a name="API_OutputDescription_Contents"></a>

 **DestinationSchema**   <a name="analytics-Type-OutputDescription-DestinationSchema"></a>
Data format used for writing data to the destination\.  
Type: [DestinationSchema](API_DestinationSchema.md) object  
Required: No

 **KinesisFirehoseOutputDescription**   <a name="analytics-Type-OutputDescription-KinesisFirehoseOutputDescription"></a>
Describes the Amazon Kinesis Firehose delivery stream configured as the destination where output is written\.  
Type: [KinesisFirehoseOutputDescription](API_KinesisFirehoseOutputDescription.md) object  
Required: No

 **KinesisStreamsOutputDescription**   <a name="analytics-Type-OutputDescription-KinesisStreamsOutputDescription"></a>
Describes Amazon Kinesis stream configured as the destination where output is written\.  
Type: [KinesisStreamsOutputDescription](API_KinesisStreamsOutputDescription.md) object  
Required: No

 **LambdaOutputDescription**   <a name="analytics-Type-OutputDescription-LambdaOutputDescription"></a>
Describes the AWS Lambda function configured as the destination where output is written\.  
Type: [LambdaOutputDescription](API_LambdaOutputDescription.md) object  
Required: No

 **Name**   <a name="analytics-Type-OutputDescription-Name"></a>
Name of the in\-application stream configured as output\.  
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 32\.  
Required: No

 **OutputId**   <a name="analytics-Type-OutputDescription-OutputId"></a>
A unique identifier for the output configuration\.  
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 50\.  
Pattern: `[a-zA-Z0-9_.-]+`   
Required: No

## See Also<a name="API_OutputDescription_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:
+  [AWS SDK for C\+\+](https://docs.aws.amazon.com/goto/SdkForCpp/kinesisanalytics-2015-08-14/OutputDescription) 
+  [AWS SDK for Go](https://docs.aws.amazon.com/goto/SdkForGoV1/kinesisanalytics-2015-08-14/OutputDescription) 
+  [AWS SDK for Java](https://docs.aws.amazon.com/goto/SdkForJava/kinesisanalytics-2015-08-14/OutputDescription) 
+  [AWS SDK for Ruby V3](https://docs.aws.amazon.com/goto/SdkForRubyV3/kinesisanalytics-2015-08-14/OutputDescription) 