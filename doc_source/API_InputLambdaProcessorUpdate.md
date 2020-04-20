# InputLambdaProcessorUpdate<a name="API_InputLambdaProcessorUpdate"></a>

Represents an update to the [InputLambdaProcessor](https://docs.aws.amazon.com/kinesisanalytics/latest/dev/API_InputLambdaProcessor.html) that is used to preprocess the records in the stream\.

## Contents<a name="API_InputLambdaProcessorUpdate_Contents"></a>

 **ResourceARNUpdate**   <a name="analytics-Type-InputLambdaProcessorUpdate-ResourceARNUpdate"></a>
The Amazon Resource Name \(ARN\) of the new [AWS Lambda](https://docs.aws.amazon.com/lambda/) function that is used to preprocess the records in the stream\.  
To specify an earlier version of the Lambda function than the latest, include the Lambda function version in the Lambda function ARN\. For more information about Lambda ARNs, see [Example ARNs: AWS Lambda](/general/latest/gr/aws-arns-and-namespaces.html#arn-syntax-lambda) 
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 2048\.  
Pattern: `arn:.*`   
Required: No

 **RoleARNUpdate**   <a name="analytics-Type-InputLambdaProcessorUpdate-RoleARNUpdate"></a>
The ARN of the new IAM role that is used to access the AWS Lambda function\.  
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 2048\.  
Pattern: `arn:aws:iam::\d{12}:role/?[a-zA-Z_0-9+=,.@\-_/]+`   
Required: No

## See Also<a name="API_InputLambdaProcessorUpdate_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:
+  [AWS SDK for C\+\+](https://docs.aws.amazon.com/goto/SdkForCpp/kinesisanalytics-2015-08-14/InputLambdaProcessorUpdate) 
+  [AWS SDK for Go](https://docs.aws.amazon.com/goto/SdkForGoV1/kinesisanalytics-2015-08-14/InputLambdaProcessorUpdate) 
+  [AWS SDK for Java](https://docs.aws.amazon.com/goto/SdkForJava/kinesisanalytics-2015-08-14/InputLambdaProcessorUpdate) 
+  [AWS SDK for Ruby V3](https://docs.aws.amazon.com/goto/SdkForRubyV3/kinesisanalytics-2015-08-14/InputLambdaProcessorUpdate) 