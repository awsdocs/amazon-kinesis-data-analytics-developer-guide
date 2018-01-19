# ApplicationDetail<a name="API_ApplicationDetail"></a>

Provides a description of the application, including the application Amazon Resource Name \(ARN\), status, latest version, and input and output configuration\.

## Contents<a name="API_ApplicationDetail_Contents"></a>

 **ApplicationARN**   
ARN of the application\.  
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 2048\.  
Pattern: `arn:.*`   
Required: Yes

 **ApplicationCode**   
Returns the application code that you provided to perform data analysis on any of the in\-application streams in your application\.  
Type: String  
Length Constraints: Minimum length of 0\. Maximum length of 51200\.  
Required: No

 **ApplicationDescription**   
Description of the application\.  
Type: String  
Length Constraints: Minimum length of 0\. Maximum length of 1024\.  
Required: No

 **ApplicationName**   
Name of the application\.  
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 128\.  
Pattern: `[a-zA-Z0-9_.-]+`   
Required: Yes

 **ApplicationStatus**   
Status of the application\.  
Type: String  
Valid Values:` DELETING | STARTING | STOPPING | READY | RUNNING | UPDATING`   
Required: Yes

 **ApplicationVersionId**   
Provides the current application version\.  
Type: Long  
Valid Range: Minimum value of 1\. Maximum value of 999999999\.  
Required: Yes

 **CloudWatchLoggingOptionDescriptions**   
Describes the CloudWatch log streams that are configured to receive application messages\. For more information about using CloudWatch log streams with Amazon Kinesis Analytics applications, see [Working with Amazon CloudWatch Logs](http://docs.aws.amazon.com/kinesisanalytics/latest/dev/cloudwatch-logs.html)\.   
Type: Array of [CloudWatchLoggingOptionDescription](API_CloudWatchLoggingOptionDescription.md) objects  
Required: No

 **CreateTimestamp**   
Time stamp when the application version was created\.  
Type: Timestamp  
Required: No

 **InputDescriptions**   
Describes the application input configuration\. For more information, see [Configuring Application Input](http://docs.aws.amazon.com/kinesisanalytics/latest/dev/how-it-works-input.html)\.   
Type: Array of [InputDescription](API_InputDescription.md) objects  
Required: No

 **LastUpdateTimestamp**   
Time stamp when the application was last updated\.  
Type: Timestamp  
Required: No

 **OutputDescriptions**   
Describes the application output configuration\. For more information, see [Configuring Application Output](http://docs.aws.amazon.com/kinesisanalytics/latest/dev/how-it-works-output.html)\.   
Type: Array of [OutputDescription](API_OutputDescription.md) objects  
Required: No

 **ReferenceDataSourceDescriptions**   
Describes reference data sources configured for the application\. For more information, see [Configuring Application Input](http://docs.aws.amazon.com/kinesisanalytics/latest/dev/how-it-works-input.html)\.   
Type: Array of [ReferenceDataSourceDescription](API_ReferenceDataSourceDescription.md) objects  
Required: No

## See Also<a name="API_ApplicationDetail_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:

+  [AWS SDK for C\+\+](http://docs.aws.amazon.com/goto/SdkForCpp/kinesisanalytics-2015-08-14/ApplicationDetail) 

+  [AWS SDK for Go](http://docs.aws.amazon.com/goto/SdkForGoV1/kinesisanalytics-2015-08-14/ApplicationDetail) 

+  [AWS SDK for Java](http://docs.aws.amazon.com/goto/SdkForJava/kinesisanalytics-2015-08-14/ApplicationDetail) 

+  [AWS SDK for Ruby V2](http://docs.aws.amazon.com/goto/SdkForRubyV2/kinesisanalytics-2015-08-14/ApplicationDetail) 