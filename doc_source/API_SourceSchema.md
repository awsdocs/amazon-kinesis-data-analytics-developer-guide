# SourceSchema<a name="API_SourceSchema"></a>

Describes the format of the data in the streaming source, and how each data element maps to corresponding columns created in the in\-application stream\.

## Contents<a name="API_SourceSchema_Contents"></a>

 **RecordColumns**   <a name="analytics-Type-SourceSchema-RecordColumns"></a>
A list of `RecordColumn` objects\.  
Type: Array of [RecordColumn](API_RecordColumn.md) objects  
Array Members: Minimum number of 1 item\. Maximum number of 1000 items\.  
Required: Yes

 **RecordEncoding**   <a name="analytics-Type-SourceSchema-RecordEncoding"></a>
Specifies the encoding of the records in the streaming source\. For example, UTF\-8\.  
Type: String  
Pattern: `UTF-8`   
Required: No

 **RecordFormat**   <a name="analytics-Type-SourceSchema-RecordFormat"></a>
Specifies the format of the records on the streaming source\.  
Type: [RecordFormat](API_RecordFormat.md) object  
Required: Yes

## See Also<a name="API_SourceSchema_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:
+  [AWS SDK for C\+\+](https://docs.aws.amazon.com/goto/SdkForCpp/kinesisanalytics-2015-08-14/SourceSchema) 
+  [AWS SDK for Go](https://docs.aws.amazon.com/goto/SdkForGoV1/kinesisanalytics-2015-08-14/SourceSchema) 
+  [AWS SDK for Java](https://docs.aws.amazon.com/goto/SdkForJava/kinesisanalytics-2015-08-14/SourceSchema) 
+  [AWS SDK for Ruby V3](https://docs.aws.amazon.com/goto/SdkForRubyV3/kinesisanalytics-2015-08-14/SourceSchema) 