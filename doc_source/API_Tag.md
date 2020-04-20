# Tag<a name="API_Tag"></a>

A key\-value pair \(the value is optional\) that you can define and assign to AWS resources\. If you specify a tag that already exists, the tag value is replaced with the value that you specify in the request\. Note that the maximum number of application tags includes system tags\. The maximum number of user\-defined application tags is 50\. For more information, see [Using Tagging](https://docs.aws.amazon.com/kinesisanalytics/latest/dev/how-tagging.html)\.

## Contents<a name="API_Tag_Contents"></a>

 **Key**   <a name="analytics-Type-Tag-Key"></a>
The key of the key\-value tag\.  
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 128\.  
Required: Yes

 **Value**   <a name="analytics-Type-Tag-Value"></a>
The value of the key\-value tag\. The value is optional\.  
Type: String  
Length Constraints: Minimum length of 0\. Maximum length of 256\.  
Required: No

## See Also<a name="API_Tag_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:
+  [AWS SDK for C\+\+](https://docs.aws.amazon.com/goto/SdkForCpp/kinesisanalytics-2015-08-14/Tag) 
+  [AWS SDK for Go](https://docs.aws.amazon.com/goto/SdkForGoV1/kinesisanalytics-2015-08-14/Tag) 
+  [AWS SDK for Java](https://docs.aws.amazon.com/goto/SdkForJava/kinesisanalytics-2015-08-14/Tag) 
+  [AWS SDK for Ruby V3](https://docs.aws.amazon.com/goto/SdkForRubyV3/kinesisanalytics-2015-08-14/Tag) 