# DiscoverInputSchema<a name="API_DiscoverInputSchema"></a>

**Note**  
This documentation is for version 1 of the Amazon Kinesis Data Analytics API, which only supports SQL applications\. Version 2 of the API supports SQL and Java applications\. For more information about version 2, see [Amazon Kinesis Data Analytics API V2 Documentation](/kinesisanalytics/latest/apiv2/Welcome.html)\.

Infers a schema by evaluating sample records on the specified streaming source \(Amazon Kinesis stream or Amazon Kinesis Firehose delivery stream\) or S3 object\. In the response, the operation returns the inferred schema and also the sample records that the operation used to infer the schema\.

 You can use the inferred schema when configuring a streaming source for your application\. For conceptual information, see [Configuring Application Input](https://docs.aws.amazon.com/kinesisanalytics/latest/dev/how-it-works-input.html)\. Note that when you create an application using the Amazon Kinesis Analytics console, the console uses this operation to infer a schema and show it in the console user interface\. 

 This operation requires permissions to perform the `kinesisanalytics:DiscoverInputSchema` action\. 

## Request Syntax<a name="API_DiscoverInputSchema_RequestSyntax"></a>

```
{
   "[InputProcessingConfiguration](#analytics-DiscoverInputSchema-request-InputProcessingConfiguration)": { 
      "[InputLambdaProcessor](API_InputProcessingConfiguration.md#analytics-Type-InputProcessingConfiguration-InputLambdaProcessor)": { 
         "[ResourceARN](API_InputLambdaProcessor.md#analytics-Type-InputLambdaProcessor-ResourceARN)": "string",
         "[RoleARN](API_InputLambdaProcessor.md#analytics-Type-InputLambdaProcessor-RoleARN)": "string"
      }
   },
   "[InputStartingPositionConfiguration](#analytics-DiscoverInputSchema-request-InputStartingPositionConfiguration)": { 
      "[InputStartingPosition](API_InputStartingPositionConfiguration.md#analytics-Type-InputStartingPositionConfiguration-InputStartingPosition)": "string"
   },
   "[ResourceARN](#analytics-DiscoverInputSchema-request-ResourceARN)": "string",
   "[RoleARN](#analytics-DiscoverInputSchema-request-RoleARN)": "string",
   "[S3Configuration](#analytics-DiscoverInputSchema-request-S3Configuration)": { 
      "[BucketARN](API_S3Configuration.md#analytics-Type-S3Configuration-BucketARN)": "string",
      "[FileKey](API_S3Configuration.md#analytics-Type-S3Configuration-FileKey)": "string",
      "[RoleARN](API_S3Configuration.md#analytics-Type-S3Configuration-RoleARN)": "string"
   }
}
```

## Request Parameters<a name="API_DiscoverInputSchema_RequestParameters"></a>

The request accepts the following data in JSON format\.

 ** [InputProcessingConfiguration](#API_DiscoverInputSchema_RequestSyntax) **   <a name="analytics-DiscoverInputSchema-request-InputProcessingConfiguration"></a>
The [InputProcessingConfiguration](https://docs.aws.amazon.com/kinesisanalytics/latest/dev/API_InputProcessingConfiguration.html) to use to preprocess the records before discovering the schema of the records\.  
Type: [InputProcessingConfiguration](API_InputProcessingConfiguration.md) object  
Required: No

 ** [InputStartingPositionConfiguration](#API_DiscoverInputSchema_RequestSyntax) **   <a name="analytics-DiscoverInputSchema-request-InputStartingPositionConfiguration"></a>
Point at which you want Amazon Kinesis Analytics to start reading records from the specified streaming source discovery purposes\.  
Type: [InputStartingPositionConfiguration](API_InputStartingPositionConfiguration.md) object  
Required: No

 ** [ResourceARN](#API_DiscoverInputSchema_RequestSyntax) **   <a name="analytics-DiscoverInputSchema-request-ResourceARN"></a>
Amazon Resource Name \(ARN\) of the streaming source\.  
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 2048\.  
Pattern: `arn:.*`   
Required: No

 ** [RoleARN](#API_DiscoverInputSchema_RequestSyntax) **   <a name="analytics-DiscoverInputSchema-request-RoleARN"></a>
ARN of the IAM role that Amazon Kinesis Analytics can assume to access the stream on your behalf\.  
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 2048\.  
Pattern: `arn:aws:iam::\d{12}:role/?[a-zA-Z_0-9+=,.@\-_/]+`   
Required: No

 ** [S3Configuration](#API_DiscoverInputSchema_RequestSyntax) **   <a name="analytics-DiscoverInputSchema-request-S3Configuration"></a>
Specify this parameter to discover a schema from data in an Amazon S3 object\.  
Type: [S3Configuration](API_S3Configuration.md) object  
Required: No

## Response Syntax<a name="API_DiscoverInputSchema_ResponseSyntax"></a>

```
{
   "[InputSchema](#analytics-DiscoverInputSchema-response-InputSchema)": { 
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
   "[ParsedInputRecords](#analytics-DiscoverInputSchema-response-ParsedInputRecords)": [ 
      [ "string" ]
   ],
   "[ProcessedInputRecords](#analytics-DiscoverInputSchema-response-ProcessedInputRecords)": [ "string" ],
   "[RawInputRecords](#analytics-DiscoverInputSchema-response-RawInputRecords)": [ "string" ]
}
```

## Response Elements<a name="API_DiscoverInputSchema_ResponseElements"></a>

If the action is successful, the service sends back an HTTP 200 response\.

The following data is returned in JSON format by the service\.

 ** [InputSchema](#API_DiscoverInputSchema_ResponseSyntax) **   <a name="analytics-DiscoverInputSchema-response-InputSchema"></a>
Schema inferred from the streaming source\. It identifies the format of the data in the streaming source and how each data element maps to corresponding columns in the in\-application stream that you can create\.  
Type: [SourceSchema](API_SourceSchema.md) object

 ** [ParsedInputRecords](#API_DiscoverInputSchema_ResponseSyntax) **   <a name="analytics-DiscoverInputSchema-response-ParsedInputRecords"></a>
An array of elements, where each element corresponds to a row in a stream record \(a stream record can have more than one row\)\.  
Type: Array of arrays of strings

 ** [ProcessedInputRecords](#API_DiscoverInputSchema_ResponseSyntax) **   <a name="analytics-DiscoverInputSchema-response-ProcessedInputRecords"></a>
Stream data that was modified by the processor specified in the `InputProcessingConfiguration` parameter\.  
Type: Array of strings

 ** [RawInputRecords](#API_DiscoverInputSchema_ResponseSyntax) **   <a name="analytics-DiscoverInputSchema-response-RawInputRecords"></a>
Raw stream data that was sampled to infer the schema\.  
Type: Array of strings

## Errors<a name="API_DiscoverInputSchema_Errors"></a>

 **InvalidArgumentException**   
Specified input parameter value is invalid\.  
HTTP Status Code: 400

 **ResourceProvisionedThroughputExceededException**   
Discovery failed to get a record from the streaming source because of the Amazon Kinesis Streams ProvisionedThroughputExceededException\. For more information, see [GetRecords](https://docs.aws.amazon.com/kinesis/latest/APIReference/API_GetRecords.html) in the Amazon Kinesis Streams API Reference\.  
HTTP Status Code: 400

 **ServiceUnavailableException**   
The service is unavailable\. Back off and retry the operation\.   
HTTP Status Code: 500

 **UnableToDetectSchemaException**   
Data format is not valid\. Amazon Kinesis Analytics is not able to detect schema for the given streaming source\.  
HTTP Status Code: 400

## See Also<a name="API_DiscoverInputSchema_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:
+  [AWS Command Line Interface](https://docs.aws.amazon.com/goto/aws-cli/kinesisanalytics-2015-08-14/DiscoverInputSchema) 
+  [AWS SDK for \.NET](https://docs.aws.amazon.com/goto/DotNetSDKV3/kinesisanalytics-2015-08-14/DiscoverInputSchema) 
+  [AWS SDK for C\+\+](https://docs.aws.amazon.com/goto/SdkForCpp/kinesisanalytics-2015-08-14/DiscoverInputSchema) 
+  [AWS SDK for Go](https://docs.aws.amazon.com/goto/SdkForGoV1/kinesisanalytics-2015-08-14/DiscoverInputSchema) 
+  [AWS SDK for Java](https://docs.aws.amazon.com/goto/SdkForJava/kinesisanalytics-2015-08-14/DiscoverInputSchema) 
+  [AWS SDK for JavaScript](https://docs.aws.amazon.com/goto/AWSJavaScriptSDK/kinesisanalytics-2015-08-14/DiscoverInputSchema) 
+  [AWS SDK for PHP V3](https://docs.aws.amazon.com/goto/SdkForPHPV3/kinesisanalytics-2015-08-14/DiscoverInputSchema) 
+  [AWS SDK for Python](https://docs.aws.amazon.com/goto/boto3/kinesisanalytics-2015-08-14/DiscoverInputSchema) 
+  [AWS SDK for Ruby V3](https://docs.aws.amazon.com/goto/SdkForRubyV3/kinesisanalytics-2015-08-14/DiscoverInputSchema) 