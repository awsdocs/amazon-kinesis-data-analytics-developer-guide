# UntagResource<a name="API_UntagResource"></a>

Removes one or more tags from a Kinesis Analytics application\. For more information, see [Using Tagging](https://docs.aws.amazon.com/kinesisanalytics/latest/dev/how-tagging.html)\.

## Request Syntax<a name="API_UntagResource_RequestSyntax"></a>

```
{
   "[ResourceARN](#analytics-UntagResource-request-ResourceARN)": "string",
   "[TagKeys](#analytics-UntagResource-request-TagKeys)": [ "string" ]
}
```

## Request Parameters<a name="API_UntagResource_RequestParameters"></a>

The request accepts the following data in JSON format\.

 ** [ResourceARN](#API_UntagResource_RequestSyntax) **   <a name="analytics-UntagResource-request-ResourceARN"></a>
The ARN of the Kinesis Analytics application from which to remove the tags\.  
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 2048\.  
Pattern: `arn:aws:kinesisanalytics:[a-z]{2}-[a-z]+-\d{1}+:\d{12}+:application/[a-zA-Z0-9_.-]{1,128}`   
Required: Yes

 ** [TagKeys](#API_UntagResource_RequestSyntax) **   <a name="analytics-UntagResource-request-TagKeys"></a>
A list of keys of tags to remove from the specified application\.  
Type: Array of strings  
Array Members: Minimum number of 1 item\. Maximum number of 200 items\.  
Length Constraints: Minimum length of 1\. Maximum length of 128\.  
Required: Yes

## Response Elements<a name="API_UntagResource_ResponseElements"></a>

If the action is successful, the service sends back an HTTP 200 response with an empty HTTP body\.

## Errors<a name="API_UntagResource_Errors"></a>

 **ConcurrentModificationException**   
Exception thrown as a result of concurrent modification to an application\. For example, two individuals attempting to edit the same application at the same time\.  
HTTP Status Code: 400

 **InvalidArgumentException**   
Specified input parameter value is invalid\.  
HTTP Status Code: 400

 **ResourceInUseException**   
Application is not available for this operation\.  
HTTP Status Code: 400

 **ResourceNotFoundException**   
Specified application can't be found\.  
HTTP Status Code: 400

 **TooManyTagsException**   
Application created with too many tags, or too many tags added to an application\. Note that the maximum number of application tags includes system tags\. The maximum number of user\-defined application tags is 50\.  
HTTP Status Code: 400

## See Also<a name="API_UntagResource_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:
+  [AWS Command Line Interface](https://docs.aws.amazon.com/goto/aws-cli/kinesisanalytics-2015-08-14/UntagResource) 
+  [AWS SDK for \.NET](https://docs.aws.amazon.com/goto/DotNetSDKV3/kinesisanalytics-2015-08-14/UntagResource) 
+  [AWS SDK for C\+\+](https://docs.aws.amazon.com/goto/SdkForCpp/kinesisanalytics-2015-08-14/UntagResource) 
+  [AWS SDK for Go](https://docs.aws.amazon.com/goto/SdkForGoV1/kinesisanalytics-2015-08-14/UntagResource) 
+  [AWS SDK for Java](https://docs.aws.amazon.com/goto/SdkForJava/kinesisanalytics-2015-08-14/UntagResource) 
+  [AWS SDK for JavaScript](https://docs.aws.amazon.com/goto/AWSJavaScriptSDK/kinesisanalytics-2015-08-14/UntagResource) 
+  [AWS SDK for PHP V3](https://docs.aws.amazon.com/goto/SdkForPHPV3/kinesisanalytics-2015-08-14/UntagResource) 
+  [AWS SDK for Python](https://docs.aws.amazon.com/goto/boto3/kinesisanalytics-2015-08-14/UntagResource) 
+  [AWS SDK for Ruby V3](https://docs.aws.amazon.com/goto/SdkForRubyV3/kinesisanalytics-2015-08-14/UntagResource) 