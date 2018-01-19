# RecordColumn<a name="API_RecordColumn"></a>

Describes the mapping of each data element in the streaming source to the corresponding column in the in\-application stream\.

Also used to describe the format of the reference data source\.

## Contents<a name="API_RecordColumn_Contents"></a>

 **Mapping**   
Reference to the data element in the streaming input of the reference data source\.  
Type: String  
Required: No

 **Name**   
Name of the column created in the in\-application input stream or reference table\.  
Type: String  
Pattern: `[a-zA-Z_][a-zA-Z0-9_]*`   
Required: Yes

 **SqlType**   
Type of column created in the in\-application input stream or reference table\.  
Type: String  
Length Constraints: Minimum length of 1\.  
Required: Yes

## See Also<a name="API_RecordColumn_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:

+  [AWS SDK for C\+\+](http://docs.aws.amazon.com/goto/SdkForCpp/kinesisanalytics-2015-08-14/RecordColumn) 

+  [AWS SDK for Go](http://docs.aws.amazon.com/goto/SdkForGoV1/kinesisanalytics-2015-08-14/RecordColumn) 

+  [AWS SDK for Java](http://docs.aws.amazon.com/goto/SdkForJava/kinesisanalytics-2015-08-14/RecordColumn) 

+  [AWS SDK for Ruby V2](http://docs.aws.amazon.com/goto/SdkForRubyV2/kinesisanalytics-2015-08-14/RecordColumn) 