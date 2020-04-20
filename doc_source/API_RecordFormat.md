# RecordFormat<a name="API_RecordFormat"></a>

 Describes the record format and relevant mapping information that should be applied to schematize the records on the stream\. 

## Contents<a name="API_RecordFormat_Contents"></a>

 **MappingParameters**   <a name="analytics-Type-RecordFormat-MappingParameters"></a>
When configuring application input at the time of creating or updating an application, provides additional mapping information specific to the record format \(such as JSON, CSV, or record fields delimited by some delimiter\) on the streaming source\.  
Type: [MappingParameters](API_MappingParameters.md) object  
Required: No

 **RecordFormatType**   <a name="analytics-Type-RecordFormat-RecordFormatType"></a>
The type of record format\.  
Type: String  
Valid Values:` JSON | CSV`   
Required: Yes

## See Also<a name="API_RecordFormat_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:
+  [AWS SDK for C\+\+](https://docs.aws.amazon.com/goto/SdkForCpp/kinesisanalytics-2015-08-14/RecordFormat) 
+  [AWS SDK for Go](https://docs.aws.amazon.com/goto/SdkForGoV1/kinesisanalytics-2015-08-14/RecordFormat) 
+  [AWS SDK for Java](https://docs.aws.amazon.com/goto/SdkForJava/kinesisanalytics-2015-08-14/RecordFormat) 
+  [AWS SDK for Ruby V3](https://docs.aws.amazon.com/goto/SdkForRubyV3/kinesisanalytics-2015-08-14/RecordFormat) 