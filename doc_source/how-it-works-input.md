# Configuring Application Input<a name="how-it-works-input"></a>

Your Amazon Kinesis Data Analytics application can receive input from a single streaming source and, optionally, use one reference data source\. For more information, see [Amazon Kinesis Data Analytics for SQL Applications: How It Works](how-it-works.md)\. The sections in this topic describe the application input sources\.

**Topics**
+ [Configuring a Streaming Source](#source-streaming)
+ [Configuring a Reference Source](#source-reference)
+ [Working with JSONPath](about-json-path.md)
+ [Mapping Streaming Source Elements to SQL Input Columns](sch-mapping.md)
+ [Using the Schema Discovery Feature on Streaming Data](sch-dis.md)
+ [Using the Schema Discovery Feature on Static Data](sch-dis-ref.md)
+ [Preprocessing Data Using a Lambda Function](lambda-preprocessing.md)
+ [Parallelizing Input Streams for Increased Throughput](input-parallelism.md)

## Configuring a Streaming Source<a name="source-streaming"></a>

At the time that you create an application, you specify a streaming source\. You can also modify an input after you create the application\. Amazon Kinesis Data Analytics supports the following streaming sources for your application:
+ A Kinesis data stream 
+ A Kinesis Data Firehose delivery stream

**Note**  
If the Kinesis data stream is encrypted, Kinesis Data Analytics accesses the data in the encrypted stream seamlessly with no further configuration needed\. Kinesis Data Analytics does not store unencrypted data read from Kinesis Data Streams\. For more information, see [What Is Server\-Side Encryption For Kinesis Data Streams?](https://docs.aws.amazon.com/streams/latest/dev/what-is-sse.html)\.

Kinesis Data Analytics continuously polls the streaming source for new data and ingests it in in\-application streams according to the input configuration\. 

**Note**  
Adding a Kinesis Stream as your application's input does not affect the data in the stream\. If another resource such as a Kinesis Data Firehose delivery stream also accessed the same Kinesis stream, both the Kinesis Data Firehose delivery stream and the Kinesis Data Analytics application would receive the same data\. Throughput and throttling might be affected, however\.

Your application code can query the in\-application stream\. As part of input configuration you provide the following:
+ **Streaming source** – You provide the Amazon Resource Name \(ARN\) of the stream and an IAM role that Kinesis Data Analytics can assume to access the stream on your behalf\. 
+ **In\-application stream name prefix** – When you start the application, Kinesis Data Analytics creates the specified in\-application stream\. In your application code, you access the in\-application stream using this name\. 

  You can optionally map a streaming source to multiple in\-application streams\. For more information, see [Limits](limits.md)\. In this case, Amazon Kinesis Data Analytics creates the specified number of in\-application streams with names as follows: *prefix*`_001`, *prefix*`_002`, and *prefix*`_003`\. By default, Kinesis Data Analytics maps the streaming source to one in\-application stream named *prefix*`_001`\.

  There is a limit on the rate that you can insert rows in an in\-application stream\. Therefore, Kinesis Data Analytics supports multiple such in\-application streams so that you can bring records into your application at a much faster rate\. If you find that your application is not keeping up with the data in the streaming source, you can add units of parallelism to improve performance\. 
+ **Mapping schema** – You describe the record format \(JSON, CSV\) on the streaming source\. You also describe how each record on the stream maps to columns in the in\-application stream that is created\. This is where you provide column names and data types\. 

**Note**  
Kinesis Data Analytics adds quotation marks around the identifiers \(stream name and column names\) when creating the input in\-application stream\. When querying this stream and the columns, you must specify them in quotation marks using the same casing \(matching lowercase and uppercase letters exactly\)\. For more information about identifiers, see [Identifiers](https://docs.aws.amazon.com/kinesisanalytics/latest/sqlref/sql-reference-identifiers.html) in the *Amazon Kinesis Data Analytics SQL Reference*\.

You can create an application and configure inputs in the Amazon Kinesis Data Analytics console\. The console then makes the necessary API calls\. You can configure application input when you create a new application API or add input configuration to an existing application\. For more information, see [CreateApplication](API_CreateApplication.md) and [AddApplicationInput](API_AddApplicationInput.md)\. The following is the input configuration part of the `Createapplication` API request body:

```
 "Inputs": [
        {
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
            "Name": "string"
        }
    ]
```

## Configuring a Reference Source<a name="source-reference"></a>

You can also optionally add a reference data source to an existing application to enrich the data coming in from streaming sources\. You must store reference data as an object in your Amazon S3 bucket\. When the application starts, Amazon Kinesis Data Analytics reads the Amazon S3 object and creates an in\-application reference table\. Your application code can then join it with an in\-application stream\. 

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

You can add an application reference data source either with the API or with the console\. Amazon Kinesis Data Analytics provides the following API actions to manage reference data sources:
+  [AddApplicationReferenceDataSource](API_AddApplicationReferenceDataSource.md)
+ [UpdateApplication](API_UpdateApplication.md)

For information about adding reference data using the console, see [Example: Adding Reference Data to a Kinesis Data Analytics Application](app-add-reference-data.md)\.

Note the following:
+ If the application is running, Kinesis Data Analytics creates an in\-application reference table, and then loads the reference data immediately\.
+ If the application is not running \(for example, it's in the ready state\), Kinesis Data Analytics saves only the updated input configuration\. When the application starts running, Kinesis Data Analytics loads the reference data in your application as a table\.

Suppose that you want to refresh the data after Kinesis Data Analytics creates the in\-application reference table\. Perhaps you updated the Amazon S3 object, or you want to use a different Amazon S3 object\. In this case, you can either explicitly call [UpdateApplication](API_UpdateApplication.md), or choose **Actions**, **Synchronize reference data table** in the console\. Kinesis Data Analytics does not refresh the in\-application reference table automatically\. 

There is a limit on the size of the Amazon S3 object that you can create as a reference data source\. For more information, see [Limits](limits.md)\. If the object size exceeds the limit, Kinesis Data Analytics can't load the data\. The application state appears as running, but the data is not being read\.

When you add a reference data source, you provide the following information: 
+ **S3 bucket and object key name** – In addition to the bucket name and object key, you also provide an IAM role that Kinesis Data Analytics can assume to read the object on your behalf\. 
+ **In\-application reference table name** – Kinesis Data Analytics creates this in\-application table and populates it by reading the Amazon S3 object\. This is the table name you specify in your application code\.
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