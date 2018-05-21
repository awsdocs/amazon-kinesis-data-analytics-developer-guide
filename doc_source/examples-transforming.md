# Examples: Transforming Data<a name="examples-transforming"></a>

There are times when your application code must preprocess incoming records before performing any analytics in Amazon Kinesis Data Analytics\. This can happen for various reasons, such as when records don't conform to the supported record formats, resulting in unnormalized columns in the in\-application input streams\. 

This section provides examples of how to use the available string functions to normalize data, how to extract information that you need from string columns, and so on\. The section also points to date time functions that you might find useful\. 

## Preprocessing Streams with Lambda<a name="examples-transforming-lambda"></a>

For information about preprocessing streams with AWS Lambda, see [Preprocessing Data Using a Lambda Function](lambda-preprocessing.md)\.

**Topics**
+ [Preprocessing Streams with Lambda](#examples-transforming-lambda)
+ [Examples: Transforming String Values](examples-transforming-strings.md)
+ [Example: Transforming DateTime Values](app-string-datetime-manipulation.md)
+ [Example: Transforming Multiple Data Types](app-tworecordtypes.md)