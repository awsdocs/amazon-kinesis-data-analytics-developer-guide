# Mapping Streaming Source Elements to SQL Input Columns<a name="sch-mapping"></a>

With Amazon Kinesis Data Analytics, you can process and analyze streaming data in either JSON or CSV formats using standard SQL\. 
+ To process and analyze streaming CSV data, you assign column names and data types for the columns of the input stream\. Your application imports one column from the input stream per column definition, in order\. 

  You don't have to include all of the columns in the application input stream, but you cannot skip columns from the source stream\. For example, you can import the first three columns from an input stream containing five elements, but you cannot import only columns 1, 2, and 4\.
+ To process and analyze streaming JSON data, you use JSONPath expressions to map JSON elements from a streaming source to SQL columns in an input stream\. For more information about using JSONPath with Amazon Kinesis Data Analytics, see [Working with JSONPath](about-json-path.md)\. The columns in the SQL table have data types that are mapped from JSON types\. For supported data types, see [Data Types](https://docs.aws.amazon.com/kinesisanalytics/latest/sqlref/sql-reference-data-types.html)\. For details about converting JSON data to SQL data, see [Mapping JSON Data Types to SQL Data Types](#sch-mapping-datatypes)\.

For more information about how to configure input streams, see [Configuring Application Input](how-it-works-input.md)\.

## Mapping JSON Data to SQL Columns<a name="sch-mapping-json"></a>

 You can map JSON elements to input columns using the AWS Management Console or the Kinesis Data Analytics API\. 
+ To map elements to columns using the console, see [Working with the Schema Editor](console-summary-edit-schema.md)\.
+ To map elements to columns using the Kinesis Data Analytics API, see the following section\.

To map JSON elements to columns in the in\-application input stream, you need a schema with the following information for each column:
+ **Source Expression:** The JSONPath expression that identifies the location of the data for the column\.
+ **Column Name:** The name that your SQL queries use to reference the data\.
+ **Data Type: **The SQL data type for the column\.

## Using the API<a name="sf-map-api"></a>

To map elements from a streaming source to input columns, you can use the Kinesis Data Analytics API [CreateApplication](API_CreateApplication.md) action\. To create the in\-application stream, specify a schema to transform your data into a schematized version used in SQL\. The [CreateApplication](API_CreateApplication.md) action configures your application to receive input from a single streaming source\. To map JSON elements or CSV columns to SQL columns, you create a [RecordColumn](API_RecordColumn.md) object in the [SourceSchema](API_SourceSchema.md) `RecordColumns` array\. The [RecordColumn](API_RecordColumn.md) object has the following schema:

```
{ 
    "Mapping": "String",
    "Name": "String",
    "SqlType": "String"
}
```

The fields in the [RecordColumn](API_RecordColumn.md) object have the following values:
+ `Mapping`: The JSONPath expression that identifies the location of the data in the input stream record\. This value is not present for an input schema for a source stream in CSV format\.
+ `Name`: The column name in the in\-application SQL data stream\.
+ `SqlType`: The data type of the data in the in\-application SQL data stream\.

### JSON Input Schema Example<a name="sf-map-api-json-example"></a>

The following example demonstrates the format of the `InputSchema` value for a JSON schema\.

```
"InputSchema": {
    "RecordColumns": [
        {
            "SqlType": "VARCHAR(4)",
            "Name": "TICKER_SYMBOL",
            "Mapping": "$.TICKER_SYMBOL"
        },
        {
            "SqlType": "VARCHAR(16)",
            "Name": "SECTOR",
            "Mapping": "$.SECTOR"
        },
        {
            "SqlType": "TINYINT",
            "Name": "CHANGE",
            "Mapping": "$.CHANGE"
        },
        {
            "SqlType": "DECIMAL(5,2)",
            "Name": "PRICE",
            "Mapping": "$.PRICE"
        }
    ],
    "RecordFormat": {
        "MappingParameters": {
            "JSONMappingParameters": {
                "RecordRowPath": "$"
            }
        },
        "RecordFormatType": "JSON"
    },
    "RecordEncoding": "UTF-8"
}
```

### CSV Input Schema Example<a name="sf-map-api-csv-example"></a>

The following example demonstrates the format of the `InputSchema` value for a schema in comma\-separated value \(CSV\) format\.

```
"InputSchema": {
    "RecordColumns": [
        {
            "SqlType": "VARCHAR(16)",
            "Name": "LastName"
        },
        {
            "SqlType": "VARCHAR(16)",
            "Name": "FirstName"
        },
        {
            "SqlType": "INTEGER",
             "Name": "CustomerId"
        }
    ],
    "RecordFormat": {
        "MappingParameters": {
            "CSVMappingParameters": {
                "RecordColumnDelimiter": ",",
                "RecordRowDelimiter": "\n"
            }
        },
        "RecordFormatType": "CSV"
    },
    "RecordEncoding": "UTF-8"
}
```

## Mapping JSON Data Types to SQL Data Types<a name="sch-mapping-datatypes"></a>

JSON data types are converted to corresponding SQL data types according to the application's input schema\. For information about supported SQL data types, see [Data Types](https://docs.aws.amazon.com/kinesisanalytics/latest/sqlref/sql-reference-data-types.html)\. Amazon Kinesis Data Analytics converts JSON data types to SQL data types according to the following rules\.

### Null Literal<a name="sch-mapping-datatypes-null"></a>

A null literal in the JSON input stream \(that is, `"City":null`\) converts to a SQL null regardless of destination data type\.

### Boolean Literal<a name="sch-mapping-datatypes-boolean"></a>

A Boolean literal in the JSON input stream \(that is, `"Contacted":true`\) converts to SQL data as follows:
+ Numeric \(DECIMAL, INT, and so on\): `true` converts to 1; `false` converts to 0\.
+ Binary \(BINARY or VARBINARY\): 
  + `true`: Result has lowest bit set and remaining bits cleared\.
  + `false`: Result has all bits cleared\.

  Conversion to VARBINARY results in a value 1 byte in length\.
+ BOOLEAN: Converts to the corresponding SQL BOOLEAN value\.
+ Character \(CHAR or VARCHAR\): Converts to the corresponding string value \(`true` or `false`\)\. The value is truncated to fit the length of the field\.
+ Datetime \(DATE, TIME, or TIMESTAMP\): Conversion fails and a coercion error is written to the error stream\.

### Number<a name="sch-mapping-datatypes-number"></a>

A number literal in the JSON input stream \(that is, `"CustomerId":67321`\) converts to SQL data as follows:
+ Numeric \(DECIMAL, INT, and so on\): Converts directly\. If the converted value exceeds the size or precision of the target data type \(that is, converting `123.4` to INT\), conversion fails and a coercion error is written to the error stream\. 
+ Binary \(BINARY or VARBINARY\): Conversion fails and a coercion error is written to the error stream\.
+ BOOLEAN: 
  + `0`: Converts to `false`\.
  + All other numbers: Converts to `true`\.
+ Character \(CHAR or VARCHAR\): Converts to a string representation of the number\.
+ Datetime \(DATE, TIME, or TIMESTAMP\): Conversion fails and a coercion error is written to the error stream\.

### String<a name="sch-mapping-datatypes-string"></a>

A string value in the JSON input stream \(that is, `"CustomerName":"John Doe"`\) converts to SQL data as follows:
+ Numeric \(DECIMAL, INT, and so on\): Amazon Kinesis Data Analytics attempts to convert the value to the target data type\. If the value cannot be converted, conversion fails and a coercion error is written to the error stream\.
+ Binary \(BINARY or VARBINARY\): If the source string is a valid binary literal \(that is, `X'3F67A23A'`, with an even number of f\), the value is converted to the target data type\. Otherwise, conversion fails and a coercion error is written to the error stream\.
+ BOOLEAN: If the source string is `"true"`, converts to `true`\. This comparison is case\-insensitive\. Otherwise, converts to `false`\.
+ Character \(CHAR or VARCHAR\): Converts to the string value in the input\. If the value is longer than the target data type, it is truncated and no error is written to the error stream\.
+ Datetime \(DATE, TIME, or TIMESTAMP\): If the source string is in a format that can be converted to the target value, the value is converted\. Otherwise, conversion fails and a coercion error is written to the error stream\.

  Valid datetime formats include:
  + "1992\-02\-14"
  + "1992\-02\-14 18:35:44\.0"

### Array or Object<a name="sch-mapping-datatypes-array"></a>

An array or object in the JSON input stream converts to SQL data as follows:
+ Character \(CHAR or VARCHAR\): Converts to the source text of the array or object\. See [Accessing Arrays](about-json-path.md#about-json-path-arrays)\.
+ All other data types: Conversion fails and a coercion error is written to the error stream\.

For an example of a JSON array, see [Working with JSONPath](about-json-path.md)\.

## Related Topics<a name="sch-mapping.Related"></a>
+ [Configuring Application Input](how-it-works-input.md)
+ [Data Types](https://docs.aws.amazon.com/kinesisanalytics/latest/sqlref/sql-reference-data-types.html)
+ [Working with the Schema Editor](console-summary-edit-schema.md)
+ [CreateApplication](API_CreateApplication.md)
+ [RecordColumn](API_RecordColumn.md)
+ [SourceSchema](API_SourceSchema.md)