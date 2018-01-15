# Example: Counting Distinct Values \(the COUNT\_DISTINCT\_ITEMS\_TUMBLING function\)<a name="count-distinct-items-example"></a>

In this exercise, you set up a Amazon Kinesis Data Analytics application to count distinct values in a one\-minute tumbling window\. 

For the exercise, you use the demo stream, which provides continuous flow of simulated stock trade records and finds distinct stocks traded in a one\-minute window\.

Use the following application code:

```
CREATE OR REPLACE STREAM DESTINATION_SQL_STREAM (
               NUMBER_OF_DISTINCT_ITEMS BIGINT);

CREATE OR REPLACE PUMP "STREAM_PUMP" AS 
   INSERT INTO "DESTINATION_SQL_STREAM"
      SELECT STREAM * 
      FROM TABLE(COUNT_DISTINCT_ITEMS_TUMBLING(
                        CURSOR(SELECT STREAM * FROM "SOURCE_SQL_STREAM_001"),
                        'column1', -- name of column in single quotes
                         60          -- tumbling window size in seconds
  )
);
```

The code uses the `COUNT_DISTINCT_ITEMS_TUMBLING` function to approximate the number of distinct values\. For more information about the function, see [COUNT\_DISTINCT\_ITEMS\_TUMBLING Function](http://docs.aws.amazon.com/kinesisanalytics/latest/sqlref/count-distinct-items.html) in the *Amazon Kinesis Data Analytics SQL Reference*\. 

In the console, this application code is available as a template \(**Approximate distinct count**\), which you use to quickly create the application\. You need to update this template code by replacing '*column1*' with `'TICKER_SYMBOL'` to estimate the number of distinct stocks traded, in a one\-minute tumbling window\.

You can use the following procedure to test this template using the demo stream\.

**To create an application**

1. Complete the getting started exercise\. For instructions, see [ Step 3: Create Your Starter Amazon Kinesis Data Analytics Application](get-started-exercise.md)\.

1. Now you replace the application code in the SQL editor by the **Approximate distinct count** template as follows\. In SQL editor, do the following: 

   1. Delete the existing sample code\.

   1. Choose **Add SQL from templates** and then select the **Approximate distinct count** template\. 

   1. Update the template code by replacing the column name from *column1* to 'TICKER\_SYMBOL' \(with single quotes around\)\. 

1. Save and run SQL\. Review results in the **Real\-time analytics** tab in the SQL editor\.

   Because the window size is one minute, you need to wait to see the results\. The `DESTINATION_SQL_STREAM` shows two columns \(`ROWTIME` and `NUMBER_OF_DISTINCT_ITEMS`\)\. The query emits results every one minute\.