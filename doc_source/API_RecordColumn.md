# RecordColumn<a name="API_RecordColumn"></a>

Describes the mapping of each data element in the streaming source to the corresponding column in the in\-application stream\.

Also used to describe the format of the reference data source\.

## Contents<a name="API_RecordColumn_Contents"></a>

 **Mapping**   <a name="analytics-Type-RecordColumn-Mapping"></a>
Reference to the data element in the streaming input or the reference data source\. This element is required if the [RecordFormat:RecordFormatType](API_RecordFormat.md#analytics-Type-RecordFormat-RecordFormatType) is `JSON`\.  
Type: String  
Required: No

 **Name**   <a name="analytics-Type-RecordColumn-Name"></a>
Name of the column created in the in\-application input stream or reference table\.  
Type: String  
Pattern: `[a-zA-Z_][a-zA-Z0-9_]*`   
Required: Yes

 **SqlType**   <a name="analytics-Type-RecordColumn-SqlType"></a>
Type of column created in the in\-application input stream or reference table\.  
Type: String  
Length Constraints: Minimum length of 1\.  
Required: Yes

## See Also<a name="API_RecordColumn_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:
+  [AWS SDK for C\+\+](https://docs.aws.amazon.com/goto/SdkForCpp/kinesisanalytics-2015-08-14/RecordColumn) 
+  [AWS SDK for Go](https://docs.aws.amazon.com/goto/SdkForGoV1/kinesisanalytics-2015-08-14/RecordColumn) 
+  [AWS SDK for Go \- Pilot](https://docs.aws.amazon.com/goto/SdkForGoPilot/kinesisanalytics-2015-08-14/RecordColumn) 
+  [AWS SDK for Java](https://docs.aws.amazon.com/goto/SdkForJava/kinesisanalytics-2015-08-14/RecordColumn) 
+  [AWS SDK for Ruby V2](https://docs.aws.amazon.com/goto/SdkForRubyV2/kinesisanalytics-2015-08-14/RecordColumn) 