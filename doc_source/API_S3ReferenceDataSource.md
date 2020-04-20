# S3ReferenceDataSource<a name="API_S3ReferenceDataSource"></a>

Identifies the S3 bucket and object that contains the reference data\. Also identifies the IAM role Amazon Kinesis Analytics can assume to read this object on your behalf\.

An Amazon Kinesis Analytics application loads reference data only once\. If the data changes, you call the [UpdateApplication](https://docs.aws.amazon.com/kinesisanalytics/latest/dev/API_UpdateApplication.html) operation to trigger reloading of data into your application\.

## Contents<a name="API_S3ReferenceDataSource_Contents"></a>

 **BucketARN**   <a name="analytics-Type-S3ReferenceDataSource-BucketARN"></a>
Amazon Resource Name \(ARN\) of the S3 bucket\.  
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 2048\.  
Pattern: `arn:.*`   
Required: Yes

 **FileKey**   <a name="analytics-Type-S3ReferenceDataSource-FileKey"></a>
Object key name containing reference data\.  
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 1024\.  
Required: Yes

 **ReferenceRoleARN**   <a name="analytics-Type-S3ReferenceDataSource-ReferenceRoleARN"></a>
ARN of the IAM role that the service can assume to read data on your behalf\. This role must have permission for the `s3:GetObject` action on the object and trust policy that allows Amazon Kinesis Analytics service principal to assume this role\.  
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 2048\.  
Pattern: `arn:aws:iam::\d{12}:role/?[a-zA-Z_0-9+=,.@\-_/]+`   
Required: Yes

## See Also<a name="API_S3ReferenceDataSource_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:
+  [AWS SDK for C\+\+](https://docs.aws.amazon.com/goto/SdkForCpp/kinesisanalytics-2015-08-14/S3ReferenceDataSource) 
+  [AWS SDK for Go](https://docs.aws.amazon.com/goto/SdkForGoV1/kinesisanalytics-2015-08-14/S3ReferenceDataSource) 
+  [AWS SDK for Java](https://docs.aws.amazon.com/goto/SdkForJava/kinesisanalytics-2015-08-14/S3ReferenceDataSource) 
+  [AWS SDK for Ruby V3](https://docs.aws.amazon.com/goto/SdkForRubyV3/kinesisanalytics-2015-08-14/S3ReferenceDataSource) 