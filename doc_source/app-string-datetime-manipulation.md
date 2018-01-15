# Example: Manipulating Strings and Date Times<a name="app-string-datetime-manipulation"></a>

## String Manipulation<a name="string-manipulation"></a>

Amazon Kinesis Data Analytics supports formats such as JSON and CSV for records on a streaming source\. For details, see [RecordFormat](API_RecordFormat.md)\. These records then map to rows in in\-application stream as per the input configuration\. For details, see [Configuring Application Input](how-it-works-input.md)\. The input configuration specifies how record fields in the streaming source map to columns in in\-application stream\. 

This mapping works when records on the streaming source follow the supported formats, which results in an in\-application stream with normalized data\. 

But, what if data on your streaming source does not conform to supported standards? For example, what if your streaming source contains data such as clickstream data, IoT sensors, and application logs? Consider these examples:

+ **Streaming source contains application logs** – The application logs follow the standard Apache log format, and are written to the stream using JSON format\. 

  ```
  {
     "Log":"192.168.254.30 - John [24/May/2004:22:01:02 -0700] "GET /icons/apache_pb.gif HTTP/1.1" 304 0"
  }
  ```

  For more information about the standard Apache log format, see [Log Files](https://httpd.apache.org/docs/2.4/logs.html) on the Apache website\. 

   

+ **Streaming source contains semi\-structured data** – The following example shows two records\. The `Col_E_Unstrucutured` field value is a series of comma\-separated values\.

  ```
  { "Col_A" : "string",
    "Col_B" : "string",
    "Col_C" : "string",
    "Col_D" : "string",
    "Col_E_Unstructured" : "value,value,value,value"}
  
  { "Col_A" : "string",
    "Col_B" : "string",
    "Col_C" : "string",
    "Col_D" : "string",
    "Col_E_Unstructured" : "value,value,value,value"}
  ```

  There are five columns, the first four have string type values and the last column contains comma\-separated values\.

+ Records on your streaming source contain URLs and you need a portion of the URL domain name for analytics\.

  ```
  { "referrer" : "http://www.amazon.com"}
  { "referrer" : "http://www.stackoverflow.com" }
  ```

In such cases, the following two\-step process generally works for creating in\-application streams that contain normalized data:

1. Configure application input to map the unstructured field to a column of the `VARCHAR(N)` type in the in\-application input stream that is created\.

1. In your application code, use string functions to split this single column into multiple columns and then save the rows in another in\-application stream\. This in\-application stream that your application code creates will have normalized data\. You can then perform analytics on this in\-application stream\.

Amazon Kinesis Data Analytics provides string operations, standard SQL functions, and extensions to the SQL standard for working with string columns, including the following: 

+ **String operators** – Operators such as `LIKE` and `SIMILAR` are useful in comparing strings\. For more information, see [String Operators](http://docs.aws.amazon.com/kinesisanalytics/latest/sqlref/sql-reference-string-operators.html) in the *Amazon Kinesis Data Analytics SQL Reference*\.

   

+ **SQL functions** – The following functions are useful when manipulating individual strings\. For more information, see [Scalar Functions](http://docs.aws.amazon.com/kinesisanalytics/latest/sqlref/sql-reference-scalar-functions.html) in the *Amazon Kinesis Data Analytics SQL Reference*\.

  + CHAR\_LENGTH – Provides the length of a string\. 

  + LOWER/UPPER – Converts a string to lowercase or uppercase\. 

  + OVERLAY – Replace a portion of the first string argument \(the original string\) with the second string argument \(the replacement string\)\.

  + SUBSTRING – Extracts a portion of a source string starting at a specific position\. 

  + POSITION – Searches for a string within another string\. 

     

+ **SQL Extensions** – These are useful for working with unstructured strings such as logs and URIs\.

  + REGEX\_LOG\_PARSE – Parses a string based on default Java Regular Expression patterns\.

  + FAST\_REGEX\_LOG\_PARSER – Works similar to the regex parser, but takes several shortcuts to ensure faster results\. For example, the fast regex parser stops at the first match it finds \(known as *lazy semantics*\)\.

  + W3C\_Log\_Parse – A function for quickly formatting Apache logs\.

  + FIXED\_COLUMN\_LOG\_PARSE – Parses fixed\-width fields and automatically converts them to the given SQL types\.

  + VARIABLE\_COLUMN\_LOG\_PARSE – Splits an input string into fields separated by a delimiter character or a delimiter string\.

For examples using these functions, see the following topics:

+ [Example: String Manipulation \(W3C\_LOG\_PARSE Function\) ](string-manipulation-example-1.md)

+ [Example: String Manipulation \(VARIABLE\_COLUMN\_LOG\_PARSE Function\) ](string-manipulation-example-2.md)

+ [Example: String Manipulation \(SUBSTRING Function\)](string-manipulation-example-3.md)

## Date Time Manipulation<a name="datetime-manipulation"></a>

Amazon Kinesis Data Analytics supports converting columns to time stamps\. For example, you might want to use your own time stamp as part of a `GROUP BY` clause as another time\-based window, in addition to the `ROWTIME` column\. Kinesis Data Analytics provides operations and SQL functions for working with date and time fields\. 

+ **Date and time operators** – You can perform arithmetic operations on dates, times, and interval data types\. For more information, see [Date, Timestamp, and Interval Operators](http://docs.aws.amazon.com/kinesisanalytics/latest/sqlref/sql-reference-date-timestamp-interval.html) in the *Amazon Kinesis Data Analytics SQL Reference*\.

   

+ **SQL Functions** – These include the following:

  + `EXTRACT()` – Extracts one field from a date, time, time stamp, or interval expression\.

  + `CURRENT_TIME` – Returns the time when the query executes \(UTC\)\.

  + `CURRENT_DATE` – Returns the date when the query executes \(UTC\)\.

  + `CURRENT_TIMESTAMP` – Returns the time stamp when the query executes \(UTC\)\.

  + `LOCALTIME` – Returns the current time when the query executes as defined by the environment on which Amazon Kinesis Data Analytics is running \(UTC\)\.

  + `LOCALTIMESTAMP` – Returns the current time stamp as defined by the environment on which Amazon Kinesis Data Analytics is running \(UTC\)\.

     

+ **SQL Extensions** – These include the following: 

  + `CURRENT_ROW_TIMESTAMP` – Returns a new time stamp for each row in the stream\. 

  + `TSDIFF` – Returns the difference of two time stamps in milliseconds\.

  + `CHAR_TO_DATE` – Converts a string to a date\.

  + `CHAR_TO_TIME` – Converts a string to time\.

  + `CHAR_TO_TIMESTAMP` – Converts a string to a time stamp\.

  + `DATE_TO_CHAR` – Converts a date to a string\.

  + `TIME_TO_CHAR` – Converts a time to a string\.

  + `TIMESTAMP_TO_CHAR` – Converts a time stamp to a string\.

Most of the preceding SQL functions use a format to convert the columns\. The format is flexible\. For example, you can specify the format `yyyy-MM-dd hh:mm:ss` to convert an input string `2009-09-16 03:15:24` into a time stamp\. For more information, [Char To Timestamp\(Sys\)](http://docs.aws.amazon.com/kinesisanalytics/latest/sqlref/sql-reference-char-to-timestamp.html) in the *Amazon Kinesis Data Analytics SQL Reference*\. 