# ApplicationDetail<a name="API_ApplicationDetail"></a>

**Note**  
This documentation is for version 1 of the Amazon Kinesis Data Analytics API, which only supports SQL applications\. Version 2 of the API supports SQL and Java applications\. For more information about version 2, see [Amazon Kinesis Data Analytics API V2 Documentation](/kinesisanalytics/latest/apiv2/Welcome.html)\.

Provides a description of the application, including the application Amazon Resource Name \(ARN\), status, latest version, and input and output configuration\.

## Contents<a name="API_ApplicationDetail_Contents"></a>

 **ApplicationARN**   <a name="analytics-Type-ApplicationDetail-ApplicationARN"></a>
ARN of the application\.  
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 2048\.  
Pattern: `arn:.*`   
Required: Yes

 **ApplicationCode**   <a name="analytics-Type-ApplicationDetail-ApplicationCode"></a>
Returns the application code that you provided to perform data analysis on any of the in\-application streams in your application\.  
Type: String  
Length Constraints: Minimum length of 0\. Maximum length of 102400\.  
Required: No

 **ApplicationDescription**   <a name="analytics-Type-ApplicationDetail-ApplicationDescription"></a>
Description of the application\.  
Type: String  
Length Constraints: Minimum length of 0\. Maximum length of 1024\.  
Required: No

 **ApplicationName**   <a name="analytics-Type-ApplicationDetail-ApplicationName"></a>
Name of the application\.  
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 128\.  
Pattern: `[a-zA-Z0-9_.-]+`   
Required: Yes

 **ApplicationStatus**   <a name="analytics-Type-ApplicationDetail-ApplicationStatus"></a>
Status of the application\.  
Type: String  
Valid Values:` DELETING | STARTING | STOPPING | READY | RUNNING | UPDATING`   
Required: Yes

 **ApplicationVersionId**   <a name="analytics-Type-ApplicationDetail-ApplicationVersionId"></a>
Provides the current application version\.  
Type: Long  
Valid Range: Minimum value of 1\. Maximum value of 999999999\.  
Required: Yes

 **CloudWatchLoggingOptionDescriptions**   <a name="analytics-Type-ApplicationDetail-CloudWatchLoggingOptionDescriptions"></a>
Describes the CloudWatch log streams that are configured to receive application messages\. For more information about using CloudWatch log streams with Amazon Kinesis Analytics applications, see [Working with Amazon CloudWatch Logs](https://docs.aws.amazon.com/kinesisanalytics/latest/dev/cloudwatch-logs.html)\.   
Type: Array of [CloudWatchLoggingOptionDescription](API_CloudWatchLoggingOptionDescription.md) objects  
Required: No

 **CreateTimestamp**   <a name="analytics-Type-ApplicationDetail-CreateTimestamp"></a>
Time stamp when the application version was created\.  
Type: Timestamp  
Required: No

 **InputDescriptions**   <a name="analytics-Type-ApplicationDetail-InputDescriptions"></a>
Describes the application input configuration\. For more information, see [Configuring Application Input](https://docs.aws.amazon.com/kinesisanalytics/latest/dev/how-it-works-input.html)\.   
Type: Array of [InputDescription](API_InputDescription.md) objects  
Required: No

 **LastUpdateTimestamp**   <a name="analytics-Type-ApplicationDetail-LastUpdateTimestamp"></a>
Time stamp when the application was last updated\.  
Type: Timestamp  
Required: No

 **OutputDescriptions**   <a name="analytics-Type-ApplicationDetail-OutputDescriptions"></a>
Describes the application output configuration\. For more information, see [Configuring Application Output](https://docs.aws.amazon.com/kinesisanalytics/latest/dev/how-it-works-output.html)\.   
Type: Array of [OutputDescription](API_OutputDescription.md) objects  
Required: No

 **ReferenceDataSourceDescriptions**   <a name="analytics-Type-ApplicationDetail-ReferenceDataSourceDescriptions"></a>
Describes reference data sources configured for the application\. For more information, see [Configuring Application Input](https://docs.aws.amazon.com/kinesisanalytics/latest/dev/how-it-works-input.html)\.   
Type: Array of [ReferenceDataSourceDescription](API_ReferenceDataSourceDescription.md) objects  
Required: No

## See Also<a name="API_ApplicationDetail_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:
+  [AWS SDK for C\+\+](https://docs.aws.amazon.com/goto/SdkForCpp/kinesisanalytics-2015-08-14/ApplicationDetail) 
+  [AWS SDK for Go](https://docs.aws.amazon.com/goto/SdkForGoV1/kinesisanalytics-2015-08-14/ApplicationDetail) 
+  [AWS SDK for Java](https://docs.aws.amazon.com/goto/SdkForJava/kinesisanalytics-2015-08-14/ApplicationDetail) 
+  [AWS SDK for Ruby V3](https://docs.aws.amazon.com/goto/SdkForRubyV3/kinesisanalytics-2015-08-14/ApplicationDetail) 