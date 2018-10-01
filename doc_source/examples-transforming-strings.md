# Examples: Transforming String Values<a name="examples-transforming-strings"></a>

Amazon Kinesis Data Analytics supports formats such as JSON and CSV for records on a streaming source\. For details, see [RecordFormat](API_RecordFormat.md)\. These records then map to rows in an in\-application stream as per the input configuration\. For details, see [Configuring Application Input](how-it-works-input.md)\. The input configuration specifies how record fields in the streaming source map to columns in an in\-application stream\. 

This mapping works when records on the streaming source follow the supported formats, which results in an in\-application stream with normalized data\. But what if data on your streaming source does not conform to supported standards? For example, what if your streaming source contains data such as clickstream data, IoT sensors, and application logs? 

Consider these examples:
+ Streaming source contains application logs – The application logs follow the standard Apache log format, and are written to the stream using JSON format\. 

  ```
  {
     "Log":"192.168.254.30 - John [24/May/2004:22:01:02 -0700] "GET /icons/apache_pb.gif HTTP/1.1" 304 0"
  }
  ```

  For more information about the standard Apache log format, see [Log Files](https://httpd.apache.org/docs/2.4/logs.html) on the Apache website\. 

   
+ Streaming source contains semi\-structured data – The following example shows two records\. The `Col_E_Unstructured` field value is a series of comma\-separated values\. There are five columns: the first four have string type values, and the last column contains comma\-separated values\.

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
+ Records on your streaming source contain URLs, and you need a portion of the URL domain name for analytics\.

  ```
  { "referrer" : "http://www.amazon.com"}
  { "referrer" : "http://www.stackoverflow.com" }
  ```

In such cases, the following two\-step process generally works for creating in\-application streams that contain normalized data:

1. Configure application input to map the unstructured field to a column of the `VARCHAR(N)` type in the in\-application input stream that is created\.

1. In your application code, use string functions to split this single column into multiple columns and then save the rows in another in\-application stream\. This in\-application stream that your application code creates will have normalized data\. You can then perform analytics on this in\-application stream\.

Amazon Kinesis Data Analytics provides the following string operations, standard SQL functions, and extensions to the SQL standard for working with string columns: 
+ **String operators** – Operators such as `LIKE` and `SIMILAR` are useful in comparing strings\. For more information, see [String Operators](https://docs.aws.amazon.com/kinesisanalytics/latest/sqlref/sql-reference-string-operators.html) in the *Amazon Kinesis Data Analytics SQL Reference*\.
+ **SQL functions** – The following functions are useful when manipulating individual strings\. For more information, see [String and Search Functions](https://docs.aws.amazon.com/kinesisanalytics/latest/sqlref/sql-reference-string-and-search-functions.html) in the *Amazon Kinesis Data Analytics SQL Reference*\.
  + `CHAR_LENGTH` – Provides the length of a string\. 
  + `INITCAP` – Returns a converted version of the input string such that the first character of each space\-delimited word is uppercase, and all other characters are lowercase\. 
  + `LOWER/UPPER` – Converts a string to lowercase or uppercase\. 
  + `OVERLAY` – Replaces a portion of the first string argument \(the original string\) with the second string argument \(the replacement string\)\.
  + `POSITION` – Searches for a string within another string\. 
  + `REGEX_REPLACE` – Replaces a substring with an alternative substring\.
  + `SUBSTRING` – Extracts a portion of a source string starting at a specific position\. 
  + `TRIM` – Removes instances of the specified character from the beginning or end of the source string\. 
+ **SQL extensions** – These are useful for working with unstructured strings such as logs and URIs\. For more information, see [Log Parsing Functions](https://docs.aws.amazon.com/kinesisanalytics/latest/sqlref/sql-reference-pattern-matching-functions.html) in the *Amazon Kinesis Data Analytics SQL Reference*\.
  + `FAST_REGEX_LOG_PARSER` – Works similar to the regex parser, but it takes several shortcuts to ensure faster results\. For example, the fast regex parser stops at the first match it finds \(known as *lazy semantics*\)\.
  + `FIXED_COLUMN_LOG_PARSE` – Parses fixed\-width fields and automatically converts them to the given SQL types\.
  + `REGEX_LOG_PARSE` – Parses a string based on default Java regular expression patterns\.
  + `SYS_LOG_PARSE` – Parses entries commonly found in UNIX/Linux system logs\.
  + `VARIABLE_COLUMN_LOG_PARSE` – Splits an input string into fields separated by a delimiter character or a delimiter string\.
  + `W3C_LOG_PARSE` – Can be used for quickly formatting Apache logs\.

For examples using these functions, see the following topics:

**Topics**
+ [Example: Extracting a Portion of a String \(SUBSTRING Function\)](examples-transforming-strings-substring.md)
+ [Example: Replacing a Substring using Regex \(REGEX\_REPLACE Function\)](examples-transforming-strings-regexreplace.md)
+ [Example: Parsing Log Strings Based on Regular Expressions \(REGEX\_LOG\_PARSE Function\)](examples-transforming-strings-regexlogparse.md)
+ [Example: Parsing Web Logs \(W3C\_LOG\_PARSE Function\)](examples-transforming-strings-w3clogparse.md)
+ [Example: Split Strings into Multiple Fields \(VARIABLE\_COLUMN\_LOG\_PARSE Function\)](examples-transforming-strings-variablecolumnlogparse.md)