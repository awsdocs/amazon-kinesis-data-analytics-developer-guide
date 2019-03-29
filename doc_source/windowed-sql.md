# Windowed Queries<a name="windowed-sql"></a>

SQL queries in your application code execute continuously over in\-application streams\. An in\-application stream represents unbounded data that flows continuously through your application\. Therefore, to get result sets from this continuously updating input, you often bound queries using a window defined in terms of time or rows\. These are also called *windowed SQL*\. 

For a time\-based windowed query, you specify the window size in terms of time \(for example, a one\-minute window\)\. This requires a timestamp column in your in\-application stream that is monotonically increasing\. \(The timestamp for a new row is greater than or equal to the previous row\.\) Amazon Kinesis Data Analytics provides such a timestamp column called `ROWTIME` for each in\-application stream\. You can use this column when specifying time\-based queries\. For your application, you might choose some other timestamp option\. For more information, see [Timestamps and the ROWTIME Column](timestamps-rowtime-concepts.md)\.

For a row\-based windowed query, you specify the window size in terms of the number of rows\.

You can specify a query to process records in a tumbling window, sliding window, or stagger window manner, depending on your application needs\. Kinesis Data Analytics supports the following window types:
+ [Stagger Windows](stagger-window-concepts.md): A query that aggregates data using keyed time\-based windows that open as data arrives\. The keys allow for multiple overlapping windows\. This is the recommended way to aggregate data using time\-based windows, because Stagger Windows reduce late or out\-of\-order data compared to Tumbling windows\.
+ [Tumbling Windows](tumbling-window-concepts.md): A query that aggregates data using distinct time\-based windows that open and close at regular intervals\.
+ [Sliding Windows](sliding-window-concepts.md): A query that aggregates data continuously, using a fixed time or rowcount interval\.