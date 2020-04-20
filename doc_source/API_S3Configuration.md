# S3Configuration<a name="API_S3Configuration"></a>

Provides a description of an Amazon S3 data source, including the Amazon Resource Name \(ARN\) of the S3 bucket, the ARN of the IAM role that is used to access the bucket, and the name of the Amazon S3 object that contains the data\.

## Contents<a name="API_S3Configuration_Contents"></a>

 **BucketARN**   <a name="analytics-Type-S3Configuration-BucketARN"></a>
ARN of the S3 bucket that contains the data\.  
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 2048\.  
Pattern: `arn:.*`   
Required: Yes

 **FileKey**   <a name="analytics-Type-S3Configuration-FileKey"></a>
The name of the object that contains the data\.  
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 1024\.  
Required: Yes

 **RoleARN**   <a name="analytics-Type-S3Configuration-RoleARN"></a>
IAM ARN of the role used to access the data\.  
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 2048\.  
Pattern: `arn:aws:iam::\d{12}:role/?[a-zA-Z_0-9+=,.@\-_/]+`   
Required: Yes

## See Also<a name="API_S3Configuration_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:
+  [AWS SDK for C\+\+](https://docs.aws.amazon.com/goto/SdkForCpp/kinesisanalytics-2015-08-14/S3Configuration) 
+  [AWS SDK for Go](https://docs.aws.amazon.com/goto/SdkForGoV1/kinesisanalytics-2015-08-14/S3Configuration) 
+  [AWS SDK for Java](https://docs.aws.amazon.com/goto/SdkForJava/kinesisanalytics-2015-08-14/S3Configuration) 
+  [AWS SDK for Ruby V3](https://docs.aws.amazon.com/goto/SdkForRubyV3/kinesisanalytics-2015-08-14/S3Configuration) 