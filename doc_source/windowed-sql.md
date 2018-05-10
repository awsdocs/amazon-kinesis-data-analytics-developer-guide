# Windowed Queries<a name="windowed-sql"></a>

SQL queries in your application code execute continuously over in\-application streams\. And, an in\-application stream represents unbounded data that is flowing continuously through your application\. Therefore, to get result sets from this continuously updating input, you often bound queries using a window defined in terms of time or rows\. These are also called *windowed SQL*\. 

For a time\-based windowed query, you specify the window size in terms of time \(for example, a one\-minute window\)\. This requires a timestamp column in your in\-application stream that is monotonically increasing \(timestamp for a new row is greater than or equal to previous row\)\. Amazon Kinesis Data Analytics provides such a timestamp column called `ROWTIME` for each in\-application stream\. You can use this column when specifying time\-based queries\. For your application, you might choose some other timestamp option\. For more information, see [Timestamps and the ROWTIME Column](timestamps-rowtime-concepts.md)\.

For a row\-based windowed query, you specify window size in terms of the number of rows\.

You can specify a query to process records in a tumbling window or sliding window manner, depending on your application needs\. For more information, see the following topics:
+ [Tumbling Windows \(Aggregations Using GROUP BY\)](tumbling-window-concepts.md)
+ [Sliding Windows](sliding-window-concepts.md)