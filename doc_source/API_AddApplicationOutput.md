# AddApplicationOutput<a name="API_AddApplicationOutput"></a>

Adds an external destination to your Amazon Kinesis Analytics application\.

If you want Amazon Kinesis Analytics to deliver data from an in\-application stream within your application to an external destination \(such as an Amazon Kinesis stream, an Amazon Kinesis Firehose delivery stream, or an AWS Lambda function\), you add the relevant configuration to your application using this operation\. You can configure one or more outputs for your application\. Each output configuration maps an in\-application stream and an external destination\.

 You can use one of the output configurations to deliver data from your in\-application error stream to an external destination so that you can analyze the errors\. For more information, see [Understanding Application Output \(Destination\)](http://docs.aws.amazon.com/kinesisanalytics/latest/dev/how-it-works-output.html)\. 

 Any configuration update, including adding a streaming source using this operation, results in a new version of the application\. You can use the [DescribeApplication](API_DescribeApplication.md) operation to find the current application version\.

For the limits on the number of application inputs and outputs you can configure, see [Limits](http://docs.aws.amazon.com/kinesisanalytics/latest/dev/limits.html)\.

This operation requires permissions to perform the `kinesisanalytics:AddApplicationOutput` action\.

## Request Syntax<a name="API_AddApplicationOutput_RequestSyntax"></a>

```
{
   "ApplicationName": "string",
   "CurrentApplicationVersionId": number,
   "Output": { 
      "DestinationSchema": { 
         "RecordFormatType": "string"
      },
      "KinesisFirehoseOutput": { 
         "ResourceARN": "string",
         "RoleARN": "string"
      },
      "KinesisStreamsOutput": { 
         "ResourceARN": "string",
         "RoleARN": "string"
      },
      "LambdaOutput": { 
         "ResourceARN": "string",
         "RoleARN": "string"
      },
      "Name": "string"
   }
}
```

## Request Parameters<a name="API_AddApplicationOutput_RequestParameters"></a>

The request accepts the following data in JSON format\.

 ** ApplicationName **   
Name of the application to which you want to add the output configuration\.  
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 128\.  
Pattern: `[a-zA-Z0-9_.-]+`   
Required: Yes

 ** CurrentApplicationVersionId **   
Version of the application to which you want to add the output configuration\. You can use the [DescribeApplication](API_DescribeApplication.md) operation to get the current application version\. If the version specified is not the current version, the `ConcurrentModificationException` is returned\.   
Type: Long  
Valid Range: Minimum value of 1\. Maximum value of 999999999\.  
Required: Yes

 ** Output **   
An array of objects, each describing one output configuration\. In the output configuration, you specify the name of an in\-application stream, a destination \(that is, an Amazon Kinesis stream, an Amazon Kinesis Firehose delivery stream, or an AWS Lambda function\), and record the formation to use when writing to the destination\.  
Type: [Output](API_Output.md) object  
Required: Yes

## Response Elements<a name="API_AddApplicationOutput_ResponseElements"></a>

If the action is successful, the service sends back an HTTP 200 response with an empty HTTP body\.

## Errors<a name="API_AddApplicationOutput_Errors"></a>

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

## See Also<a name="API_AddApplicationOutput_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:

+  [AWS Command Line Interface](http://docs.aws.amazon.com/goto/aws-cli/kinesisanalytics-2015-08-14/AddApplicationOutput) 

+  [AWS SDK for \.NET](http://docs.aws.amazon.com/goto/DotNetSDKV3/kinesisanalytics-2015-08-14/AddApplicationOutput) 

+  [AWS SDK for C\+\+](http://docs.aws.amazon.com/goto/SdkForCpp/kinesisanalytics-2015-08-14/AddApplicationOutput) 

+  [AWS SDK for Go](http://docs.aws.amazon.com/goto/SdkForGoV1/kinesisanalytics-2015-08-14/AddApplicationOutput) 

+  [AWS SDK for Java](http://docs.aws.amazon.com/goto/SdkForJava/kinesisanalytics-2015-08-14/AddApplicationOutput) 

+  [AWS SDK for JavaScript](http://docs.aws.amazon.com/goto/AWSJavaScriptSDK/kinesisanalytics-2015-08-14/AddApplicationOutput) 

+  [AWS SDK for PHP V3](http://docs.aws.amazon.com/goto/SdkForPHPV3/kinesisanalytics-2015-08-14/AddApplicationOutput) 

+  [AWS SDK for Python](http://docs.aws.amazon.com/goto/boto3/kinesisanalytics-2015-08-14/AddApplicationOutput) 

+  [AWS SDK for Ruby V2](http://docs.aws.amazon.com/goto/SdkForRubyV2/kinesisanalytics-2015-08-14/AddApplicationOutput) 