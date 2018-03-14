# ApplicationSummary<a name="API_ApplicationSummary"></a>

Provides application summary information, including the application Amazon Resource Name \(ARN\), name, and status\.

## Contents<a name="API_ApplicationSummary_Contents"></a>

 **ApplicationARN**   <a name="analytics-Type-ApplicationSummary-ApplicationARN"></a>
ARN of the application\.  
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 2048\.  
Pattern: `arn:.*`   
Required: Yes

 **ApplicationName**   <a name="analytics-Type-ApplicationSummary-ApplicationName"></a>
Name of the application\.  
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 128\.  
Pattern: `[a-zA-Z0-9_.-]+`   
Required: Yes

 **ApplicationStatus**   <a name="analytics-Type-ApplicationSummary-ApplicationStatus"></a>
Status of the application\.  
Type: String  
Valid Values:` DELETING | STARTING | STOPPING | READY | RUNNING | UPDATING`   
Required: Yes

## See Also<a name="API_ApplicationSummary_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:

+  [AWS SDK for C\+\+](http://docs.aws.amazon.com/goto/SdkForCpp/kinesisanalytics-2015-08-14/ApplicationSummary) 

+  [AWS SDK for Go](http://docs.aws.amazon.com/goto/SdkForGoV1/kinesisanalytics-2015-08-14/ApplicationSummary) 

+  [AWS SDK for Java](http://docs.aws.amazon.com/goto/SdkForJava/kinesisanalytics-2015-08-14/ApplicationSummary) 

+  [AWS SDK for Ruby V2](http://docs.aws.amazon.com/goto/SdkForRubyV2/kinesisanalytics-2015-08-14/ApplicationSummary) 