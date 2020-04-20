# S3ReferenceDataSourceUpdate<a name="API_S3ReferenceDataSourceUpdate"></a>

Describes the S3 bucket name, object key name, and IAM role that Amazon Kinesis Analytics can assume to read the Amazon S3 object on your behalf and populate the in\-application reference table\.

## Contents<a name="API_S3ReferenceDataSourceUpdate_Contents"></a>

 **BucketARNUpdate**   <a name="analytics-Type-S3ReferenceDataSourceUpdate-BucketARNUpdate"></a>
Amazon Resource Name \(ARN\) of the S3 bucket\.  
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 2048\.  
Pattern: `arn:.*`   
Required: No

 **FileKeyUpdate**   <a name="analytics-Type-S3ReferenceDataSourceUpdate-FileKeyUpdate"></a>
Object key name\.  
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 1024\.  
Required: No

 **ReferenceRoleARNUpdate**   <a name="analytics-Type-S3ReferenceDataSourceUpdate-ReferenceRoleARNUpdate"></a>
ARN of the IAM role that Amazon Kinesis Analytics can assume to read the Amazon S3 object and populate the in\-application\.  
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 2048\.  
Pattern: `arn:aws:iam::\d{12}:role/?[a-zA-Z_0-9+=,.@\-_/]+`   
Required: No

## See Also<a name="API_S3ReferenceDataSourceUpdate_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:
+  [AWS SDK for C\+\+](https://docs.aws.amazon.com/goto/SdkForCpp/kinesisanalytics-2015-08-14/S3ReferenceDataSourceUpdate) 
+  [AWS SDK for Go](https://docs.aws.amazon.com/goto/SdkForGoV1/kinesisanalytics-2015-08-14/S3ReferenceDataSourceUpdate) 
+  [AWS SDK for Java](https://docs.aws.amazon.com/goto/SdkForJava/kinesisanalytics-2015-08-14/S3ReferenceDataSourceUpdate) 
+  [AWS SDK for Ruby V3](https://docs.aws.amazon.com/goto/SdkForRubyV3/kinesisanalytics-2015-08-14/S3ReferenceDataSourceUpdate) 