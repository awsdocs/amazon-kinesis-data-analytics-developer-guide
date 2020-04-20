# CSVMappingParameters<a name="API_CSVMappingParameters"></a>

Provides additional mapping information when the record format uses delimiters, such as CSV\. For example, the following sample records use CSV format, where the records use the *'\\n'* as the row delimiter and a comma \(","\) as the column delimiter: 

 `"name1", "address1"` 

 `"name2", "address2"` 

## Contents<a name="API_CSVMappingParameters_Contents"></a>

 **RecordColumnDelimiter**   <a name="analytics-Type-CSVMappingParameters-RecordColumnDelimiter"></a>
Column delimiter\. For example, in a CSV format, a comma \(","\) is the typical column delimiter\.  
Type: String  
Length Constraints: Minimum length of 1\.  
Required: Yes

 **RecordRowDelimiter**   <a name="analytics-Type-CSVMappingParameters-RecordRowDelimiter"></a>
Row delimiter\. For example, in a CSV format, *'\\n'* is the typical row delimiter\.  
Type: String  
Length Constraints: Minimum length of 1\.  
Required: Yes

## See Also<a name="API_CSVMappingParameters_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:
+  [AWS SDK for C\+\+](https://docs.aws.amazon.com/goto/SdkForCpp/kinesisanalytics-2015-08-14/CSVMappingParameters) 
+  [AWS SDK for Go](https://docs.aws.amazon.com/goto/SdkForGoV1/kinesisanalytics-2015-08-14/CSVMappingParameters) 
+  [AWS SDK for Java](https://docs.aws.amazon.com/goto/SdkForJava/kinesisanalytics-2015-08-14/CSVMappingParameters) 
+  [AWS SDK for Ruby V3](https://docs.aws.amazon.com/goto/SdkForRubyV3/kinesisanalytics-2015-08-14/CSVMappingParameters) 