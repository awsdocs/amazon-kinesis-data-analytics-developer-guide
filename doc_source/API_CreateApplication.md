# CreateApplication<a name="API_CreateApplication"></a>

 Creates an Amazon Kinesis Analytics application\. You can configure each application with one streaming source as input, application code to process the input, and up to three destinations where you want Amazon Kinesis Analytics to write the output data from your application\. For an overview, see [How it Works](http://docs.aws.amazon.com/kinesisanalytics/latest/dev/how-it-works.html)\. 

In the input configuration, you map the streaming source to an in\-application stream, which you can think of as a constantly updating table\. In the mapping, you must provide a schema for the in\-application stream and map each data column in the in\-application stream to a data element in the streaming source\.

Your application code is one or more SQL statements that read input data, transform it, and generate output\. Your application code can create one or more SQL artifacts like SQL streams or pumps\.

In the output configuration, you can configure the application to write data from in\-application streams created in your applications to up to three destinations\.

 To read data from your source stream or write data to destination streams, Amazon Kinesis Analytics needs your permissions\. You grant these permissions by creating IAM roles\. This operation requires permissions to perform the `kinesisanalytics:CreateApplication` action\. 

 For introductory exercises to create an Amazon Kinesis Analytics application, see [Getting Started](http://docs.aws.amazon.com/kinesisanalytics/latest/dev/getting-started.html)\. 

## Request Syntax<a name="API_CreateApplication_RequestSyntax"></a>

```
{
   "ApplicationCode": "string",
   "ApplicationDescription": "string",
   "ApplicationName": "string",
   "CloudWatchLoggingOptions": [ 
      { 
         "LogStreamARN": "string",
         "RoleARN": "string"
      }
   ],
   "Inputs": [ 
      { 
         "InputParallelism": { 
            "Count": number
         },
         "InputProcessingConfiguration": { 
            "InputLambdaProcessor": { 
               "ResourceARN": "string",
               "RoleARN": "string"
            }
         },
         "InputSchema": { 
            "RecordColumns": [ 
               { 
                  "Mapping": "string",
                  "Name": "string",
                  "SqlType": "string"
               }
            ],
            "RecordEncoding": "string",
            "RecordFormat": { 
               "MappingParameters": { 
                  "CSVMappingParameters": { 
                     "RecordColumnDelimiter": "string",
                     "RecordRowDelimiter": "string"
                  },
                  "JSONMappingParameters": { 
                     "RecordRowPath": "string"
                  }
               },
               "RecordFormatType": "string"
            }
         },
         "KinesisFirehoseInput": { 
            "ResourceARN": "string",
            "RoleARN": "string"
         },
         "KinesisStreamsInput": { 
            "ResourceARN": "string",
            "RoleARN": "string"
         },
         "NamePrefix": "string"
      }
   ],
   "Outputs": [ 
      { 
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
   ]
}
```

## Request Parameters<a name="API_CreateApplication_RequestParameters"></a>

The request accepts the following data in JSON format\.

 ** ApplicationCode **   
One or more SQL statements that read input data, transform it, and generate output\. For example, you can write a SQL statement that reads data from one in\-application stream, generates a running average of the number of advertisement clicks by vendor, and insert resulting rows in another in\-application stream using pumps\. For more information about the typical pattern, see [Application Code](http://docs.aws.amazon.com/kinesisanalytics/latest/dev/how-it-works-app-code.html)\.   
You can provide such series of SQL statements, where output of one statement can be used as the input for the next statement\. You store intermediate results by creating in\-application streams and pumps\.  
Note that the application code must create the streams with names specified in the `Outputs`\. For example, if your `Outputs` defines output streams named `ExampleOutputStream1` and `ExampleOutputStream2`, then your application code must create these streams\.   
Type: String  
Length Constraints: Minimum length of 0\. Maximum length of 51200\.  
Required: No

 ** ApplicationDescription **   
Summary description of the application\.  
Type: String  
Length Constraints: Minimum length of 0\. Maximum length of 1024\.  
Required: No

 ** ApplicationName **   
Name of your Amazon Kinesis Analytics application \(for example, `sample-app`\)\.  
Type: String  
Length Constraints: Minimum length of 1\. Maximum length of 128\.  
Pattern: `[a-zA-Z0-9_.-]+`   
Required: Yes

 ** CloudWatchLoggingOptions **   
Use this parameter to configure a CloudWatch log stream to monitor application configuration errors\. For more information, see [Working with Amazon CloudWatch Logs](http://docs.aws.amazon.com/kinesisanalytics/latest/dev/cloudwatch-logs.html)\.  
Type: Array of [CloudWatchLoggingOption](API_CloudWatchLoggingOption.md) objects  
Required: No

 ** Inputs **   
Use this parameter to configure the application input\.  
You can configure your application to receive input from a single streaming source\. In this configuration, you map this streaming source to an in\-application stream that is created\. Your application code can then query the in\-application stream like a table \(you can think of it as a constantly updating table\)\.  
For the streaming source, you provide its Amazon Resource Name \(ARN\) and format of data on the stream \(for example, JSON, CSV, etc\.\)\. You also must provide an IAM role that Amazon Kinesis Analytics can assume to read this stream on your behalf\.  
To create the in\-application stream, you need to specify a schema to transform your data into a schematized version used in SQL\. In the schema, you provide the necessary mapping of the data elements in the streaming source to record columns in the in\-app stream\.  
Type: Array of [Input](API_Input.md) objects  
Required: No

 ** Outputs **   
You can configure application output to write data from any of the in\-application streams to up to three destinations\.  
These destinations can be Amazon Kinesis streams, Amazon Kinesis Firehose delivery streams, AWS Lambda destinations, or any combination of the three\.  
In the configuration, you specify the in\-application stream name, the destination stream or Lambda function Amazon Resource Name \(ARN\), and the format to use when writing data\. You must also provide an IAM role that Amazon Kinesis Analytics can assume to write to the destination stream or Lambda function on your behalf\.  
In the output configuration, you also provide the output stream or Lambda function ARN\. For stream destinations, you provide the format of data in the stream \(for example, JSON, CSV\)\. You also must provide an IAM role that Amazon Kinesis Analytics can assume to write to the stream or Lambda function on your behalf\.  
Type: Array of [Output](API_Output.md) objects  
Required: No

## Response Syntax<a name="API_CreateApplication_ResponseSyntax"></a>

```
{
   "ApplicationSummary": { 
      "ApplicationARN": "string",
      "ApplicationName": "string",
      "ApplicationStatus": "string"
   }
}
```

## Response Elements<a name="API_CreateApplication_ResponseElements"></a>

If the action is successful, the service sends back an HTTP 200 response\.

The following data is returned in JSON format by the service\.

 ** ApplicationSummary **   
In response to your `CreateApplication` request, Amazon Kinesis Analytics returns a response with a summary of the application it created, including the application Amazon Resource Name \(ARN\), name, and status\.  
Type: [ApplicationSummary](API_ApplicationSummary.md) object

## Errors<a name="API_CreateApplication_Errors"></a>

 **CodeValidationException**   
User\-provided application code \(query\) is invalid\. This can be a simple syntax error\.  
HTTP Status Code: 400

 **InvalidArgumentException**   
Specified input parameter value is invalid\.  
HTTP Status Code: 400

 **LimitExceededException**   
Exceeded the number of applications allowed\.  
HTTP Status Code: 400

 **ResourceInUseException**   
Application is not available for this operation\.  
HTTP Status Code: 400

## See Also<a name="API_CreateApplication_SeeAlso"></a>

For more information about using this API in one of the language\-specific AWS SDKs, see the following:

+  [AWS Command Line Interface](http://docs.aws.amazon.com/goto/aws-cli/kinesisanalytics-2015-08-14/CreateApplication) 

+  [AWS SDK for \.NET](http://docs.aws.amazon.com/goto/DotNetSDKV3/kinesisanalytics-2015-08-14/CreateApplication) 

+  [AWS SDK for C\+\+](http://docs.aws.amazon.com/goto/SdkForCpp/kinesisanalytics-2015-08-14/CreateApplication) 

+  [AWS SDK for Go](http://docs.aws.amazon.com/goto/SdkForGoV1/kinesisanalytics-2015-08-14/CreateApplication) 

+  [AWS SDK for Java](http://docs.aws.amazon.com/goto/SdkForJava/kinesisanalytics-2015-08-14/CreateApplication) 

+  [AWS SDK for JavaScript](http://docs.aws.amazon.com/goto/AWSJavaScriptSDK/kinesisanalytics-2015-08-14/CreateApplication) 

+  [AWS SDK for PHP V3](http://docs.aws.amazon.com/goto/SdkForPHPV3/kinesisanalytics-2015-08-14/CreateApplication) 

+  [AWS SDK for Python](http://docs.aws.amazon.com/goto/boto3/kinesisanalytics-2015-08-14/CreateApplication) 

+  [AWS SDK for Ruby V2](http://docs.aws.amazon.com/goto/SdkForRubyV2/kinesisanalytics-2015-08-14/CreateApplication) 