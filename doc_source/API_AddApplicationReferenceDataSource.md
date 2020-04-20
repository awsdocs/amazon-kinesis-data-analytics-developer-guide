# AddApplicationReferenceDataSource<a name="API_AddApplicationReferenceDataSource"></a>

**Note**  
This documentation is for version 1 of the Amazon Kinesis Data Analytics API, which only supports SQL applications\. Version 2 of the API supports SQL and Java applications\. For more information about version 2, see [Amazon Kinesis Data Analytics API V2 Documentation](/kinesisanalytics/latest/apiv2/Welcome.html)\.

Adds a reference data source to an existing application\.

Amazon Kinesis Analytics reads reference data \(that is, an Amazon S3 object\) and creates an in\-application table within your application\. In the request, you provide the source \(S3 bucket name and object key name\), name of the in\-application table to create, and the necessary mapping information that describes how data in Amazon S3 object maps to columns in the resulting in\-application table\.

 For conceptual information, see [Configuring Application Input](https://docs.aws.amazon.com/kinesisanalytics/latest/dev/how-it-works-input.html)\. For the limits on data sources you can add to your application, see [Limits](https://docs.aws.amazon.com/kinesisanalytics/latest/dev/limits.html)\. 

 This operation requires permissions to perform the `kinesisanalytics:AddApplicationOutput` action\. 

## Request Syntax<a name="API_AddApplicationReferenceDataSource_RequestSyntax"></a>

```
{
   "[ApplicationName](#analytics-AddApplicationReferenceDataSource-request-ApplicationName)": "string",
   "[CurrentApplicationVersionId](#analytics-AddApplicationReferenceDataSource-request-CurrentApplicationVersionId)": number,
   "[ReferenceDataSource](#analytics-AddApplicationReferenceDataSource-request-ReferenceDataSource)": { 
      "[ReferenceSchema](API_ReferenceDataSource.md#analytics-Type-ReferenceDataSource-ReferenceSchema)": { 
         "[RecordColumns](API_SourceSchema.md#analytics-Type-SourceSchema-RecordColumns)": [ 
            { 
               "[Mapping](API_RecordColumn.md#analytics-Type-RecordColumn-Mapping)": "string",
               "[Name](API_RecordColumn.md#analytics-Type-RecordColumn-Name)": "string",
               "[SqlType](API_RecordColumn.md#analytics-Type-RecordColumn-SqlType)": "string"
            }
         ],
         "[RecordEncoding](API_SourceSchema.md#analytics-Type-SourceSchema-RecordEncoding)": "string",
         "[RecordFormat](API_SourceSchema.md#analytics-Type-SourceSchema-RecordFormat)": { 
            "[MappingParameters](API_RecordFormat.md#analytics-Type-RecordFormat-MappingParameters)": { 
               "[CSVMappingParameters](API_MappingParameters.md#analytics-Type-MappingParameters-CSVMappingParameters)": { 
                  "[RecordColumnDelimiter](API_CSVMappingParameters.md#analytics-Type-CSVMappingParameters-RecordColumnDelimiter)": "string",
                  "[RecordRowDelimiter](API_CSVMappingParameters.md#analytics-Type-CSVMappingParameters-RecordRowDelimiter)": "string"
               },
               "[JSONMappingParameters](API_MappingParameters.md#analytics-Type-MappingParameters-JSONMappingParameters)": { 
                  "[RecordRowPath](API_JSONMappingParameters.md#analytics-Type-JSONMappingParameters-RecordRowPath)": "string"
               }
            },
            "[RecordFormatType](API_RecordFormat.md#analytics-Type-RecordFormat-RecordFormatType)": "string"
         }
      },
      "[S3ReferenceDataSource](API_ReferenceDataSource.md#analytics-Type-ReferenceDataSource-S3ReferenceDataSource)": { 
         "[BucketARN](API_S3ReferenceDataSource.md#analytics-Type-S3ReferenceDataSource-BucketARN)": "string",
         "[FileKey](API_S3ReferenceDataSource.md#analytics-Type-S3ReferenceDataSource-FileKey)": "string",
         "[ReferenceRoleARN](API_S3ReferenceDataSource.md#analytics-Type-S3ReferenceDataSource-ReferenceRoleARN)": "string"
      },
      "[TableName](API_ReferenceDataSource.md#analytics-Type-ReferenceDataSource-TableName)": "string"
   }
}
```

## Request Parameters<a name="API_AddApplicationReferenceDataSource_RequestParameters"></a>

The request accepts the following data in JSON format\.

 ** [ApplicationName](#API_AddApplicationReferenceDataSource_RequestSyntax) **   <a name="analytics-AddApplicationReferenceDataSource-request-ApplicationName"></a>
Name of an existing application\.  
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 128\.  
Pattern: `[a-zA-Z0-9_.-]+`   
Required: Yes

 ** [CurrentApplicationVersionId](#API_AddApplicationReferenceDataSource_RequestSyntax) **   <a name="analytics-AddApplicationReferenceDataSource-request-CurrentApplicationVersionId"></a>
Version of the application for which you are adding the reference data source\. You can use the [DescribeApplication](https://docs.aws.amazon.com/kinesisanalytics/latest/dev/API_DescribeApplication.html) operation to get the current application version\. If the version specified is not the current version, the `ConcurrentModificationException` is returned\.  
Type: Long  
Valid Range: Minimum value of 1\. Maximum value of 999999999\.  
Required: Yes

 ** [ReferenceDataSource](#API_AddApplicationReferenceDataSource_RequestSyntax) **   <a name="analytics-AddApplicationReferenceDataSource-request-ReferenceDataSource"></a>
The reference data source can be an object in your Amazon S3 bucket\. Amazon Kinesis Analytics reads the object and copies the data into the in\-application table that is created\. You provide an S3 bucket, object key name, and the resulting in\-application table that is created\. You must also provide an IAM role with the necessary permissions that Amazon Kinesis Analytics can assume to read the object from your S3 bucket on your behalf\.  
Type: [ReferenceDataSource](API_ReferenceDataSource.md) object  
Required: Yes

## Response Elements<a name="API_AddApplicationReferenceDataSource_ResponseElements"></a>

If the action is successful, the service sends back an HTTP 200 response with an empty HTTP body\.

## Errors<a name="API_AddApplicationReferenceDataSource_Errors"></a>

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

 **UnsupportedOperationException**   
The request was rejected because a specified parameter is not supported or a specified resource is not valid for this operation\.   
HTTP Status Code: 400

## See Also<a name="API_AddApplicationReferenceDataSource_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:
+  [AWS Command Line Interface](https://docs.aws.amazon.com/goto/aws-cli/kinesisanalytics-2015-08-14/AddApplicationReferenceDataSource) 
+  [AWS SDK for \.NET](https://docs.aws.amazon.com/goto/DotNetSDKV3/kinesisanalytics-2015-08-14/AddApplicationReferenceDataSource) 
+  [AWS SDK for C\+\+](https://docs.aws.amazon.com/goto/SdkForCpp/kinesisanalytics-2015-08-14/AddApplicationReferenceDataSource) 
+  [AWS SDK for Go](https://docs.aws.amazon.com/goto/SdkForGoV1/kinesisanalytics-2015-08-14/AddApplicationReferenceDataSource) 
+  [AWS SDK for Java](https://docs.aws.amazon.com/goto/SdkForJava/kinesisanalytics-2015-08-14/AddApplicationReferenceDataSource) 
+  [AWS SDK for JavaScript](https://docs.aws.amazon.com/goto/AWSJavaScriptSDK/kinesisanalytics-2015-08-14/AddApplicationReferenceDataSource) 
+  [AWS SDK for PHP V3](https://docs.aws.amazon.com/goto/SdkForPHPV3/kinesisanalytics-2015-08-14/AddApplicationReferenceDataSource) 
+  [AWS SDK for Python](https://docs.aws.amazon.com/goto/boto3/kinesisanalytics-2015-08-14/AddApplicationReferenceDataSource) 
+  [AWS SDK for Ruby V3](https://docs.aws.amazon.com/goto/SdkForRubyV3/kinesisanalytics-2015-08-14/AddApplicationReferenceDataSource) 