# LambdaOutputUpdate<a name="API_LambdaOutputUpdate"></a>

When updating an output configuration using the [UpdateApplication](https://docs.aws.amazon.com/kinesisanalytics/latest/dev/API_UpdateApplication.html) operation, provides information about an AWS Lambda function configured as the destination\.

## Contents<a name="API_LambdaOutputUpdate_Contents"></a>

 **ResourceARNUpdate**   <a name="analytics-Type-LambdaOutputUpdate-ResourceARNUpdate"></a>
Amazon Resource Name \(ARN\) of the destination Lambda function\.  
To specify an earlier version of the Lambda function than the latest, include the Lambda function version in the Lambda function ARN\. For more information about Lambda ARNs, see [Example ARNs: AWS Lambda](/general/latest/gr/aws-arns-and-namespaces.html#arn-syntax-lambda) 
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 2048\.  
Pattern: `arn:.*`   
Required: No

 **RoleARNUpdate**   <a name="analytics-Type-LambdaOutputUpdate-RoleARNUpdate"></a>
ARN of the IAM role that Amazon Kinesis Analytics can assume to write to the destination function on your behalf\. You need to grant the necessary permissions to this role\.   
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 2048\.  
Pattern: `arn:aws:iam::\d{12}:role/?[a-zA-Z_0-9+=,.@\-_/]+`   
Required: No

## See Also<a name="API_LambdaOutputUpdate_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:
+  [AWS SDK for C\+\+](https://docs.aws.amazon.com/goto/SdkForCpp/kinesisanalytics-2015-08-14/LambdaOutputUpdate) 
+  [AWS SDK for Go](https://docs.aws.amazon.com/goto/SdkForGoV1/kinesisanalytics-2015-08-14/LambdaOutputUpdate) 
+  [AWS SDK for Java](https://docs.aws.amazon.com/goto/SdkForJava/kinesisanalytics-2015-08-14/LambdaOutputUpdate) 
+  [AWS SDK for Ruby V3](https://docs.aws.amazon.com/goto/SdkForRubyV3/kinesisanalytics-2015-08-14/LambdaOutputUpdate) 