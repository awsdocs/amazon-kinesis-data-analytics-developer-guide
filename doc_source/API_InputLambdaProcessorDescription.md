# InputLambdaProcessorDescription<a name="API_InputLambdaProcessorDescription"></a>

An object that contains the Amazon Resource Name \(ARN\) of the [AWS Lambda](https://docs.aws.amazon.com/lambda/) function that is used to preprocess records in the stream, and the ARN of the IAM role that is used to access the AWS Lambda expression\.

## Contents<a name="API_InputLambdaProcessorDescription_Contents"></a>

 **ResourceARN**   <a name="analytics-Type-InputLambdaProcessorDescription-ResourceARN"></a>
The ARN of the [AWS Lambda](https://docs.aws.amazon.com/lambda/) function that is used to preprocess the records in the stream\.  
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 2048\.  
Pattern: `arn:.*`   
Required: No

 **RoleARN**   <a name="analytics-Type-InputLambdaProcessorDescription-RoleARN"></a>
The ARN of the IAM role that is used to access the AWS Lambda function\.  
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 2048\.  
Pattern: `arn:aws:iam::\d{12}:role/?[a-zA-Z_0-9+=,.@\-_/]+`   
Required: No

## See Also<a name="API_InputLambdaProcessorDescription_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:
+  [AWS SDK for C\+\+](https://docs.aws.amazon.com/goto/SdkForCpp/kinesisanalytics-2015-08-14/InputLambdaProcessorDescription) 
+  [AWS SDK for Go](https://docs.aws.amazon.com/goto/SdkForGoV1/kinesisanalytics-2015-08-14/InputLambdaProcessorDescription) 
+  [AWS SDK for Java](https://docs.aws.amazon.com/goto/SdkForJava/kinesisanalytics-2015-08-14/InputLambdaProcessorDescription) 
+  [AWS SDK for Ruby V3](https://docs.aws.amazon.com/goto/SdkForRubyV3/kinesisanalytics-2015-08-14/InputLambdaProcessorDescription) 