# Getting Started with Amazon Kinesis Data Analytics for Apache Flink for Python<a name="gs-python"></a>

This section introduces you to the fundamental concepts of a Kinesis Data Analytics for Apache Flink using Python and the Table API\. It describes the available options for creating and testing your applications\. It also provides instructions for installing the necessary tools to complete the tutorials in this guide and to create your first application\. 

**Topics**
+ [Getting started with Pyflink \- The Python Interpreter for Apache \| Amazon Web Services](#Videos.Python.Interpreter)
+ [Components of Kinesis Data Analytics for Flink Application](#gs-python-table-components)
+ [Prerequisites](#gs-python-prerequisites)
+ [Create and Run a Kinesis Data Analytics for Python Application](gs-python-createapp.md)
+ [Clean Up AWS Resources](gs-python-cleanup.md)

## Getting started with Pyflink \- The Python Interpreter for Apache \| Amazon Web Services<a name="Videos.Python.Interpreter"></a>

Before you begin, we encourage you to watch the following video:

## Components of Kinesis Data Analytics for Flink Application<a name="gs-python-table-components"></a>

To process data, your Kinesis Data Analytics application uses a Python application that processes input and produces output using the Apache Flink runtime\. 

Kinesis Data Analytics application has the following components:
+ **Runtime properties:** You can use *runtime properties* to configure your application without recompiling your application code\. 
+ **Table Source:** The application consumes data by using a source\. A *source* connector reads data from a Kinesis data stream, an Amazon MSK topic, or similar\. For more information, see [Table API Sources](how-table-connectors.md#how-table-connectors-source)\.
+ **Functions:** The application processes data by using one or more functions\. A *function* can transform, enrich, or aggregate data\. 
+ **Sink:** The application produces data to external sources by using sinks\. A *sink* connector writes data to a Kinesis data stream, a Kinesis Data Firehose delivery stream, an Amazon MSK topic, an Amazon S3 bucket, and so on\. For more information, see [Table API Sinks](how-table-connectors.md#how-table-connectors-sink)\.

After you create and package your application code, you upload the code package to an Amazon S3 bucket\. You then create a Kinesis Data Analytics application\. You pass in the code package location, a streaming data source, and typically a streaming or file location that receives the application's processed data\.

## Prerequisites<a name="gs-python-prerequisites"></a>

Before starting this tutorial, complete the first two steps of the [Getting Started with Amazon Kinesis Data Analytics for Apache Flink \(DataStream API\)](getting-started.md):
+ [Step 1: Set Up an AWS Account and Create an Administrator User](setting-up.md)
+ [Step 2: Set Up the AWS Command Line Interface \(AWS CLI\)](setup-awscli.md)

To get started, see [Create an Application](gs-python-createapp.md)\.