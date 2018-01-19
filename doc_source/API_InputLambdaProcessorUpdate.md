# InputLambdaProcessorUpdate<a name="API_InputLambdaProcessorUpdate"></a>

Represents an update to the [InputLambdaProcessor](API_InputLambdaProcessor.md) that is used to preprocess the records in the stream\.

## Contents<a name="API_InputLambdaProcessorUpdate_Contents"></a>

 **ResourceARNUpdate**   
The Amazon Resource Name \(ARN\) of the new [AWS Lambda](https://aws.amazon.com/documentation/lambda/) function that is used to preprocess the records in the stream\.  
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 2048\.  
Pattern: `arn:.*`   
Required: No

 **RoleARNUpdate**   
The ARN of the new IAM role that is used to access the AWS Lambda function\.  
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 2048\.  
Pattern: `arn:aws:iam::\d{12}:role/?[a-zA-Z_0-9+=,.@\-_/]+`   
Required: No

## See Also<a name="API_InputLambdaProcessorUpdate_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:

+  [AWS SDK for C\+\+](http://docs.aws.amazon.com/goto/SdkForCpp/kinesisanalytics-2015-08-14/InputLambdaProcessorUpdate) 

+  [AWS SDK for Go](http://docs.aws.amazon.com/goto/SdkForGoV1/kinesisanalytics-2015-08-14/InputLambdaProcessorUpdate) 

+  [AWS SDK for Java](http://docs.aws.amazon.com/goto/SdkForJava/kinesisanalytics-2015-08-14/InputLambdaProcessorUpdate) 

+  [AWS SDK for Ruby V2](http://docs.aws.amazon.com/goto/SdkForRubyV2/kinesisanalytics-2015-08-14/InputLambdaProcessorUpdate) 