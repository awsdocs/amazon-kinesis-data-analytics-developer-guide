# ListApplications<a name="API_ListApplications"></a>

**Note**  
This documentation is for version 1 of the Amazon Kinesis Data Analytics API, which only supports SQL applications\. Version 2 of the API supports SQL and Java applications\. For more information about version 2, see [Amazon Kinesis Data Analytics API V2 Documentation](/kinesisanalytics/latest/apiv2/Welcome.html)\.

Returns a list of Amazon Kinesis Analytics applications in your account\. For each application, the response includes the application name, Amazon Resource Name \(ARN\), and status\. If the response returns the `HasMoreApplications` value as true, you can send another request by adding the `ExclusiveStartApplicationName` in the request body, and set the value of this to the last application name from the previous response\. 

If you want detailed information about a specific application, use [DescribeApplication](https://docs.aws.amazon.com/kinesisanalytics/latest/dev/API_DescribeApplication.html)\.

This operation requires permissions to perform the `kinesisanalytics:ListApplications` action\.

## Request Syntax<a name="API_ListApplications_RequestSyntax"></a>

```
{
   "[ExclusiveStartApplicationName](#analytics-ListApplications-request-ExclusiveStartApplicationName)": "string",
   "[Limit](#analytics-ListApplications-request-Limit)": number
}
```

## Request Parameters<a name="API_ListApplications_RequestParameters"></a>

The request accepts the following data in JSON format\.

 ** [ExclusiveStartApplicationName](#API_ListApplications_RequestSyntax) **   <a name="analytics-ListApplications-request-ExclusiveStartApplicationName"></a>
Name of the application to start the list with\. When using pagination to retrieve the list, you don't need to specify this parameter in the first request\. However, in subsequent requests, you add the last application name from the previous response to get the next page of applications\.  
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 128\.  
Pattern: `[a-zA-Z0-9_.-]+`   
Required: No

 ** [Limit](#API_ListApplications_RequestSyntax) **   <a name="analytics-ListApplications-request-Limit"></a>
Maximum number of applications to list\.  
Type: Integer  
Valid Range: Minimum value of 1\. Maximum value of 50\.  
Required: No

## Response Syntax<a name="API_ListApplications_ResponseSyntax"></a>

```
{
   "[ApplicationSummaries](#analytics-ListApplications-response-ApplicationSummaries)": [ 
      { 
         "[ApplicationARN](API_ApplicationSummary.md#analytics-Type-ApplicationSummary-ApplicationARN)": "string",
         "[ApplicationName](API_ApplicationSummary.md#analytics-Type-ApplicationSummary-ApplicationName)": "string",
         "[ApplicationStatus](API_ApplicationSummary.md#analytics-Type-ApplicationSummary-ApplicationStatus)": "string"
      }
   ],
   "[HasMoreApplications](#analytics-ListApplications-response-HasMoreApplications)": boolean
}
```

## Response Elements<a name="API_ListApplications_ResponseElements"></a>

If the action is successful, the service sends back an HTTP 200 response\.

The following data is returned in JSON format by the service\.

 ** [ApplicationSummaries](#API_ListApplications_ResponseSyntax) **   <a name="analytics-ListApplications-response-ApplicationSummaries"></a>
List of `ApplicationSummary` objects\.   
Type: Array of [ApplicationSummary](API_ApplicationSummary.md) objects

 ** [HasMoreApplications](#API_ListApplications_ResponseSyntax) **   <a name="analytics-ListApplications-response-HasMoreApplications"></a>
Returns true if there are more applications to retrieve\.  
Type: Boolean

## See Also<a name="API_ListApplications_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:
+  [AWS Command Line Interface](https://docs.aws.amazon.com/goto/aws-cli/kinesisanalytics-2015-08-14/ListApplications) 
+  [AWS SDK for \.NET](https://docs.aws.amazon.com/goto/DotNetSDKV3/kinesisanalytics-2015-08-14/ListApplications) 
+  [AWS SDK for C\+\+](https://docs.aws.amazon.com/goto/SdkForCpp/kinesisanalytics-2015-08-14/ListApplications) 
+  [AWS SDK for Go](https://docs.aws.amazon.com/goto/SdkForGoV1/kinesisanalytics-2015-08-14/ListApplications) 
+  [AWS SDK for Java](https://docs.aws.amazon.com/goto/SdkForJava/kinesisanalytics-2015-08-14/ListApplications) 
+  [AWS SDK for JavaScript](https://docs.aws.amazon.com/goto/AWSJavaScriptSDK/kinesisanalytics-2015-08-14/ListApplications) 
+  [AWS SDK for PHP V3](https://docs.aws.amazon.com/goto/SdkForPHPV3/kinesisanalytics-2015-08-14/ListApplications) 
+  [AWS SDK for Python](https://docs.aws.amazon.com/goto/boto3/kinesisanalytics-2015-08-14/ListApplications) 
+  [AWS SDK for Ruby V3](https://docs.aws.amazon.com/goto/SdkForRubyV3/kinesisanalytics-2015-08-14/ListApplications) 