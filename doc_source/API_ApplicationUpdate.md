# ApplicationUpdate<a name="API_ApplicationUpdate"></a>

Describes updates to apply to an existing Amazon Kinesis Analytics application\.

## Contents<a name="API_ApplicationUpdate_Contents"></a>

 **ApplicationCodeUpdate**   <a name="analytics-Type-ApplicationUpdate-ApplicationCodeUpdate"></a>
Describes application code updates\.  
Type: String  
Length Constraints: Minimum length of 0\. Maximum length of 102400\.  
Required: No

 **CloudWatchLoggingOptionUpdates**   <a name="analytics-Type-ApplicationUpdate-CloudWatchLoggingOptionUpdates"></a>
Describes application CloudWatch logging option updates\.  
Type: Array of [CloudWatchLoggingOptionUpdate](API_CloudWatchLoggingOptionUpdate.md) objects  
Required: No

 **InputUpdates**   <a name="analytics-Type-ApplicationUpdate-InputUpdates"></a>
Describes application input configuration updates\.  
Type: Array of [InputUpdate](API_InputUpdate.md) objects  
Required: No

 **OutputUpdates**   <a name="analytics-Type-ApplicationUpdate-OutputUpdates"></a>
Describes application output configuration updates\.  
Type: Array of [OutputUpdate](API_OutputUpdate.md) objects  
Required: No

 **ReferenceDataSourceUpdates**   <a name="analytics-Type-ApplicationUpdate-ReferenceDataSourceUpdates"></a>
Describes application reference data source updates\.  
Type: Array of [ReferenceDataSourceUpdate](API_ReferenceDataSourceUpdate.md) objects  
Required: No

## See Also<a name="API_ApplicationUpdate_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:
+  [AWS SDK for C\+\+](https://docs.aws.amazon.com/goto/SdkForCpp/kinesisanalytics-2015-08-14/ApplicationUpdate) 
+  [AWS SDK for Go](https://docs.aws.amazon.com/goto/SdkForGoV1/kinesisanalytics-2015-08-14/ApplicationUpdate) 
+  [AWS SDK for Java](https://docs.aws.amazon.com/goto/SdkForJava/kinesisanalytics-2015-08-14/ApplicationUpdate) 
+  [AWS SDK for Ruby V3](https://docs.aws.amazon.com/goto/SdkForRubyV3/kinesisanalytics-2015-08-14/ApplicationUpdate) 