# Examples: Preprocessing Streams<a name="apps-preprocess-stream"></a>

There are times when your application code needs to preprocess the incoming records before performing any analytics\. This can happen for various reasons, such as records not conforming the supported record formats that can result into unnormalized columns in in\-application input streams\. This section provides examples of how to use the available string functions to normalize data, how to extract information that you need from string columns, and so on\. The section also points to date time functions that you might find useful\.

## Preprocessing Streams with Lambda<a name="apps-preprocess-stream-lambda"></a>

For information about preprocessing streams with AWS Lambda, see [Preprocessing Data Using a Lambda Function](lambda-preprocessing.md)\.

**Topics**
+ [Preprocessing Streams with Lambda](#apps-preprocess-stream-lambda)
+ [Example: Manipulating Strings and Date Times](app-string-datetime-manipulation.md)
+ [Example: Streaming Source With Multiple Record Types](app-tworecordtypes.md)
+ [Example: Adding Reference Data to an Amazon Kinesis Data Analytics Application](app-add-reference-data.md)