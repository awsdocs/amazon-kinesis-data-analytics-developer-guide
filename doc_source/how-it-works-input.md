# Configuring Application Input<a name="how-it-works-input"></a>

Your Amazon Kinesis Data Analytics application can receive input from a single streaming source and, optionally, use one reference data source\. For more information, see [Amazon Kinesis Data Analytics: How It Works](how-it-works.md)\. The sections in this topic describe the application input sources\.

## Configuring a Streaming Source<a name="source-streaming"></a>

At the time that you create an application, you specify a streaming source\. You can also modify and input after you create the application\. Amazon Kinesis Data Analytics supports the following streaming sources for your application:

+ A Kinesis stream 

+ An Amazon Kinesis Data Firehose delivery stream

Amazon Kinesis Data Analytics continuously polls the streaming source for new data and ingests it in in\-application streams according to the input configuration\. Your application code can query the in\-application stream\. As part of input configuration you provide the following:

+ **Streaming source** – You provide the Amazon Resource Name \(ARN\) of the stream and an IAM role that Amazon Kinesis Data Analytics can assume to access the stream on your behalf\. 

+ **In\-application stream name prefix** – When you start the application, Amazon Kinesis Data Analytics creates the specified in\-application stream\. In your application code, you access the in\-application stream using this name\. 

  You can optionally map a streaming source to multiple in\-application streams\. For more information, see [Limits](limits.md)\. In this case, Amazon Kinesis Data Analytics creates the specified number of in\-application streams with names as follows: *prefix*`_001`, *prefix*`_002`, and *prefix*`_003`\. By default, Amazon Kinesis Data Analytics maps the streaming source to one in\-application stream called *prefix*`_001`\.

  There is a limit on the rate that you can insert rows in an in\-application stream\. Therefore, Amazon Kinesis Data Analytics supports multiple such in\-application streams to enable you to bring records into your application at a much faster rate\. If you find your application is not keeping up with the data in the streaming source, you can add units of parallelism to improve performance\. 

+ **Mapping schema** – You describe the record format \(JSON, CSV\) on the streaming source, and describe how each record on the stream maps to columns in the in\-application stream that is created\. This is where you provide column names and data types\. 

**Note**  
Amazon Kinesis Data Analytics adds quotation marks around the identifiers \(stream name and column names\) when creating the input in\-application stream\. When querying this stream and the columns, you must specify them in quotation marks using the exact same casing \(matching lowercase and uppercase letters exactly\)\. For more information about identifiers, see [Identifiers](http://docs.aws.amazon.com/kinesisanalytics/latest/sqlref/sql-reference-identifiers.html) in the *Amazon Kinesis Data Analytics SQL Reference*\.

You can create an application and configure inputs in the Amazon Kinesis Data Analytics console\. The console then makes the necessary API calls\. You can configure application input when you create a new application API or add input configuration to an existing application\. For more information, see [CreateApplication](API_CreateApplication.md) and [AddApplicationInput](API_AddApplicationInput.md)\. The following is the input configuration part of the `Createapplication` API request body:

```
 "Inputs": [
        {
            "InputSchema": {
                "RecordColumns": [
                    {
                        "IsDropped": boolean,
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
            "Name": "string"
        }
    ]
```

## Configuring a Reference Source<a name="source-reference"></a>

You can also optionally add a reference data source to an existing application to enrich the data coming in from streaming sources\. You must store reference data as an object in your S3 bucket\. When the application starts, Amazon Kinesis Data Analytics reads the Amazon S3 object and creates an in\-application reference table\. Your application code can then join it with an in\-application stream\. 

You store reference data in the Amazon S3 object using supported formats \(CSV, JSON\)\. For example, suppose that your application performs analytics on stock orders\. Assume the following record format on the streaming source:

```
Ticker, SalePrice, OrderId

AMZN     $700        1003
XYZ      $250        1004
...
```

In this case, you might then consider maintaining a reference data source to provide details for each stock ticker, such as company name\.

```
Ticker, Company
AMZN, Amazon
XYZ, SomeCompany
...
```

Amazon Kinesis Data Analytics provides the following API actions to manage reference data sources:

+  [AddApplicationReferenceDataSource](API_AddApplicationReferenceDataSource.md)

+ [UpdateApplication](API_UpdateApplication.md)

**Note**  
Amazon Kinesis Data Analytics console does not support managing reference data sources for your applications\. You can use the AWS CLI to add reference data source to your application\. For an example, see [Example: Adding Reference Data to an Amazon Kinesis Data Analytics Application](app-add-reference-data.md)\.

Note the following:

+ If the application is running, Amazon Kinesis Data Analytics creates an in\-application reference table, and then loads the reference data immediately\.

+ If the application is not running \(for example, it's in the ready state\), Amazon Kinesis Data Analytics only saves the updated input configuration\. When the application starts running, Amazon Kinesis Data Analytics loads the reference data in your application as a table\.

If you want to refresh the data after Amazon Kinesis Data Analytics creates the in\-application reference table, perhaps because you updated the Amazon S3 object or you want to use a different Amazon S3 object, you must explicitly call the [UpdateApplication](API_UpdateApplication.md)\. Amazon Kinesis Data Analytics does not refresh the in\-application reference table automatically\. 

There is a limit on the size of the Amazon S3 object that you can create as a reference data source\. For more information, see [Limits](limits.md)\. If the object size exceeds the limit, Amazon Kinesis Data Analytics can't load the data\. The application state appears as running, but the data is not being read\.

When you add a reference data source, you provide the following information: 

+ **S3 bucket and object key name** – In addition to the bucket name and object key, you also provide an IAM role that Amazon Kinesis Data Analytics can assume to read the object on your behalf\. 

+ **In\-application reference table name** – Amazon Kinesis Data Analytics creates this in\-application table and populates it by reading the Amazon S3 object\. This is the table name you specify in your application code\.

+ **Mapping schema** – You describe the record format \(JSON, CSV\), encoding of data stored in the Amazon S3 object\. You also describe how each data element maps to columns in the in\-application reference table\. 

The following shows the request body in the `AddApplicationReferenceDataSource` API request\.

```
{
    "applicationName": "string",
    "CurrentapplicationVersionId": number,
    "ReferenceDataSource": {
        "ReferenceSchema": {
            "RecordColumns": [
                {
                    "IsDropped": boolean,
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
        "S3ReferenceDataSource": {
            "BucketARN": "string",
            "FileKey": "string",
            "ReferenceRoleARN": "string"
        },
        "TableName": "string"
    }
}
```