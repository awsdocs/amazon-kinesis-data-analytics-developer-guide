# InputStartingPositionConfiguration<a name="API_InputStartingPositionConfiguration"></a>

Describes the point at which the application reads from the streaming source\.

## Contents<a name="API_InputStartingPositionConfiguration_Contents"></a>

 **InputStartingPosition**   <a name="analytics-Type-InputStartingPositionConfiguration-InputStartingPosition"></a>
The starting position on the stream\.  
+  `NOW` \- Start reading just after the most recent record in the stream, start at the request time stamp that the customer issued\.
+  `TRIM_HORIZON` \- Start reading at the last untrimmed record in the stream, which is the oldest record available in the stream\. This option is not available for an Amazon Kinesis Firehose delivery stream\.
+  `LAST_STOPPED_POINT` \- Resume reading from where the application last stopped reading\.
Type: String  
Valid Values:` NOW | TRIM_HORIZON | LAST_STOPPED_POINT`   
Required: No

## See Also<a name="API_InputStartingPositionConfiguration_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:
+  [AWS SDK for C\+\+](https://docs.aws.amazon.com/goto/SdkForCpp/kinesisanalytics-2015-08-14/InputStartingPositionConfiguration) 
+  [AWS SDK for Go](https://docs.aws.amazon.com/goto/SdkForGoV1/kinesisanalytics-2015-08-14/InputStartingPositionConfiguration) 
+  [AWS SDK for Java](https://docs.aws.amazon.com/goto/SdkForJava/kinesisanalytics-2015-08-14/InputStartingPositionConfiguration) 
+  [AWS SDK for Ruby V3](https://docs.aws.amazon.com/goto/SdkForRubyV3/kinesisanalytics-2015-08-14/InputStartingPositionConfiguration) 