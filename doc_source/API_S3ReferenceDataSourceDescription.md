# S3ReferenceDataSourceDescription<a name="API_S3ReferenceDataSourceDescription"></a>

Provides the bucket name and object key name that stores the reference data\.

## Contents<a name="API_S3ReferenceDataSourceDescription_Contents"></a>

 **BucketARN**   <a name="analytics-Type-S3ReferenceDataSourceDescription-BucketARN"></a>
Amazon Resource Name \(ARN\) of the S3 bucket\.  
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 2048\.  
Pattern: `arn:.*`   
Required: Yes

 **FileKey**   <a name="analytics-Type-S3ReferenceDataSourceDescription-FileKey"></a>
Amazon S3 object key name\.  
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 1024\.  
Required: Yes

 **ReferenceRoleARN**   <a name="analytics-Type-S3ReferenceDataSourceDescription-ReferenceRoleARN"></a>
ARN of the IAM role that Amazon Kinesis Analytics can assume to read the Amazon S3 object on your behalf to populate the in\-application reference table\.  
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 2048\.  
Pattern: `arn:aws:iam::\d{12}:role/?[a-zA-Z_0-9+=,.@\-_/]+`   
Required: Yes

## See Also<a name="API_S3ReferenceDataSourceDescription_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:
+  [AWS SDK for C\+\+](https://docs.aws.amazon.com/goto/SdkForCpp/kinesisanalytics-2015-08-14/S3ReferenceDataSourceDescription) 
+  [AWS SDK for Go](https://docs.aws.amazon.com/goto/SdkForGoV1/kinesisanalytics-2015-08-14/S3ReferenceDataSourceDescription) 
+  [AWS SDK for Java](https://docs.aws.amazon.com/goto/SdkForJava/kinesisanalytics-2015-08-14/S3ReferenceDataSourceDescription) 
+  [AWS SDK for Ruby V3](https://docs.aws.amazon.com/goto/SdkForRubyV3/kinesisanalytics-2015-08-14/S3ReferenceDataSourceDescription) 