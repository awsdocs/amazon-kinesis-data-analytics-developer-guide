# InputConfiguration<a name="API_InputConfiguration"></a>

When you start your application, you provide this configuration, which identifies the input source and the point in the input source at which you want the application to start processing records\.

## Contents<a name="API_InputConfiguration_Contents"></a>

 **Id**   <a name="analytics-Type-InputConfiguration-Id"></a>
Input source ID\. You can get this ID by calling the [DescribeApplication](https://docs.aws.amazon.com/kinesisanalytics/latest/dev/API_DescribeApplication.html) operation\.  
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 50\.  
Pattern: `[a-zA-Z0-9_.-]+`   
Required: Yes

 **InputStartingPositionConfiguration**   <a name="analytics-Type-InputConfiguration-InputStartingPositionConfiguration"></a>
Point at which you want the application to start processing records from the streaming source\.  
Type: [InputStartingPositionConfiguration](API_InputStartingPositionConfiguration.md) object  
Required: Yes

## See Also<a name="API_InputConfiguration_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:
+  [AWS SDK for C\+\+](https://docs.aws.amazon.com/goto/SdkForCpp/kinesisanalytics-2015-08-14/InputConfiguration) 
+  [AWS SDK for Go](https://docs.aws.amazon.com/goto/SdkForGoV1/kinesisanalytics-2015-08-14/InputConfiguration) 
+  [AWS SDK for Java](https://docs.aws.amazon.com/goto/SdkForJava/kinesisanalytics-2015-08-14/InputConfiguration) 
+  [AWS SDK for Ruby V3](https://docs.aws.amazon.com/goto/SdkForRubyV3/kinesisanalytics-2015-08-14/InputConfiguration) 