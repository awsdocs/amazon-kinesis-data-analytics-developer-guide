# ApplicationUpdate<a name="API_ApplicationUpdate"></a>

Describes updates to apply to an existing Amazon Kinesis Analytics application\.

## Contents<a name="API_ApplicationUpdate_Contents"></a>

 **ApplicationCodeUpdate**   
Describes application code updates\.  
Type: String  
Length Constraints: Minimum length of 0\. Maximum length of 51200\.  
Required: No

 **CloudWatchLoggingOptionUpdates**   
Describes application CloudWatch logging option updates\.  
Type: Array of [CloudWatchLoggingOptionUpdate](API_CloudWatchLoggingOptionUpdate.md) objects  
Required: No

 **InputUpdates**   
Describes application input configuration updates\.  
Type: Array of [InputUpdate](API_InputUpdate.md) objects  
Required: No

 **OutputUpdates**   
Describes application output configuration updates\.  
Type: Array of [OutputUpdate](API_OutputUpdate.md) objects  
Required: No

 **ReferenceDataSourceUpdates**   
Describes application reference data source updates\.  
Type: Array of [ReferenceDataSourceUpdate](API_ReferenceDataSourceUpdate.md) objects  
Required: No

## See Also<a name="API_ApplicationUpdate_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:

+  [AWS SDK for C\+\+](http://docs.aws.amazon.com/goto/SdkForCpp/kinesisanalytics-2015-08-14/ApplicationUpdate) 

+  [AWS SDK for Go](http://docs.aws.amazon.com/goto/SdkForGoV1/kinesisanalytics-2015-08-14/ApplicationUpdate) 

+  [AWS SDK for Java](http://docs.aws.amazon.com/goto/SdkForJava/kinesisanalytics-2015-08-14/ApplicationUpdate) 

+  [AWS SDK for Ruby V2](http://docs.aws.amazon.com/goto/SdkForRubyV2/kinesisanalytics-2015-08-14/ApplicationUpdate) 