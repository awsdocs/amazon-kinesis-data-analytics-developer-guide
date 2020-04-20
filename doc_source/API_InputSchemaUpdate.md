# InputSchemaUpdate<a name="API_InputSchemaUpdate"></a>

Describes updates for the application's input schema\.

## Contents<a name="API_InputSchemaUpdate_Contents"></a>

 **RecordColumnUpdates**   <a name="analytics-Type-InputSchemaUpdate-RecordColumnUpdates"></a>
A list of `RecordColumn` objects\. Each object describes the mapping of the streaming source element to the corresponding column in the in\-application stream\.   
Type: Array of [RecordColumn](API_RecordColumn.md) objects  
Array Members: Minimum number of 1 item\. Maximum number of 1000 items\.  
Required: No

 **RecordEncodingUpdate**   <a name="analytics-Type-InputSchemaUpdate-RecordEncodingUpdate"></a>
Specifies the encoding of the records in the streaming source\. For example, UTF\-8\.  
Type: String  
Pattern: `UTF-8`   
Required: No

 **RecordFormatUpdate**   <a name="analytics-Type-InputSchemaUpdate-RecordFormatUpdate"></a>
Specifies the format of the records on the streaming source\.  
Type: [RecordFormat](API_RecordFormat.md) object  
Required: No

## See Also<a name="API_InputSchemaUpdate_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:
+  [AWS SDK for C\+\+](https://docs.aws.amazon.com/goto/SdkForCpp/kinesisanalytics-2015-08-14/InputSchemaUpdate) 
+  [AWS SDK for Go](https://docs.aws.amazon.com/goto/SdkForGoV1/kinesisanalytics-2015-08-14/InputSchemaUpdate) 
+  [AWS SDK for Java](https://docs.aws.amazon.com/goto/SdkForJava/kinesisanalytics-2015-08-14/InputSchemaUpdate) 
+  [AWS SDK for Ruby V3](https://docs.aws.amazon.com/goto/SdkForRubyV3/kinesisanalytics-2015-08-14/InputSchemaUpdate) 