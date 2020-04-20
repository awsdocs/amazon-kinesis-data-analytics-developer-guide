# ListTagsForResource<a name="API_ListTagsForResource"></a>

Retrieves the list of key\-value tags assigned to the application\. For more information, see [Using Tagging](https://docs.aws.amazon.com/kinesisanalytics/latest/dev/how-tagging.html)\.

## Request Syntax<a name="API_ListTagsForResource_RequestSyntax"></a>

```
{
   "[ResourceARN](#analytics-ListTagsForResource-request-ResourceARN)": "string"
}
```

## Request Parameters<a name="API_ListTagsForResource_RequestParameters"></a>

The request accepts the following data in JSON format\.

 ** [ResourceARN](#API_ListTagsForResource_RequestSyntax) **   <a name="analytics-ListTagsForResource-request-ResourceARN"></a>
The ARN of the application for which to retrieve tags\.  
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 2048\.  
Pattern: `arn:aws:kinesisanalytics:[a-z]{2}-[a-z]+-\d{1}+:\d{12}+:application/[a-zA-Z0-9_.-]{1,128}`   
Required: Yes

## Response Syntax<a name="API_ListTagsForResource_ResponseSyntax"></a>

```
{
   "[Tags](#analytics-ListTagsForResource-response-Tags)": [ 
      { 
         "[Key](API_Tag.md#analytics-Type-Tag-Key)": "string",
         "[Value](API_Tag.md#analytics-Type-Tag-Value)": "string"
      }
   ]
}
```

## Response Elements<a name="API_ListTagsForResource_ResponseElements"></a>

If the action is successful, the service sends back an HTTP 200 response\.

The following data is returned in JSON format by the service\.

 ** [Tags](#API_ListTagsForResource_ResponseSyntax) **   <a name="analytics-ListTagsForResource-response-Tags"></a>
The key\-value tags assigned to the application\.  
Type: Array of [Tag](API_Tag.md) objects  
Array Members: Minimum number of 1 item\. Maximum number of 200 items\.

## Errors<a name="API_ListTagsForResource_Errors"></a>

 **ConcurrentModificationException**   
Exception thrown as a result of concurrent modification to an application\. For example, two individuals attempting to edit the same application at the same time\.  
HTTP Status Code: 400

 **InvalidArgumentException**   
Specified input parameter value is invalid\.  
HTTP Status Code: 400

 **ResourceNotFoundException**   
Specified application can't be found\.  
HTTP Status Code: 400

## See Also<a name="API_ListTagsForResource_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:
+  [AWS Command Line Interface](https://docs.aws.amazon.com/goto/aws-cli/kinesisanalytics-2015-08-14/ListTagsForResource) 
+  [AWS SDK for \.NET](https://docs.aws.amazon.com/goto/DotNetSDKV3/kinesisanalytics-2015-08-14/ListTagsForResource) 
+  [AWS SDK for C\+\+](https://docs.aws.amazon.com/goto/SdkForCpp/kinesisanalytics-2015-08-14/ListTagsForResource) 
+  [AWS SDK for Go](https://docs.aws.amazon.com/goto/SdkForGoV1/kinesisanalytics-2015-08-14/ListTagsForResource) 
+  [AWS SDK for Java](https://docs.aws.amazon.com/goto/SdkForJava/kinesisanalytics-2015-08-14/ListTagsForResource) 
+  [AWS SDK for JavaScript](https://docs.aws.amazon.com/goto/AWSJavaScriptSDK/kinesisanalytics-2015-08-14/ListTagsForResource) 
+  [AWS SDK for PHP V3](https://docs.aws.amazon.com/goto/SdkForPHPV3/kinesisanalytics-2015-08-14/ListTagsForResource) 
+  [AWS SDK for Python](https://docs.aws.amazon.com/goto/boto3/kinesisanalytics-2015-08-14/ListTagsForResource) 
+  [AWS SDK for Ruby V3](https://docs.aws.amazon.com/goto/SdkForRubyV3/kinesisanalytics-2015-08-14/ListTagsForResource) 