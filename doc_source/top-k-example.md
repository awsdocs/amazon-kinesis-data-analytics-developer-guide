# Example: Most Frequently Occurring Values \(the TOP\_K\_ITEMS\_TUMBLING Function\)<a name="top-k-example"></a>

In this exercise, you set up an Amazon Kinesis Data Analytics application to find the top ten most frequently traded stocks in a one\-minute window\. 

For this exercise, you use the demo stream, which provides continuous flow of simulated stock trade records and finds the top ten most frequently traded stocks in a one\-minute window\.

Use the following application code:

```
CREATE OR REPLACE STREAM DESTINATION_SQL_STREAM (
             ITEM VARCHAR(1024), 
             ITEM_COUNT DOUBLE);

CREATE OR REPLACE PUMP "STREAM_PUMP" AS 
   INSERT INTO "DESTINATION_SQL_STREAM"   
      SELECT STREAM * 
      FROM TABLE(TOP_K_ITEMS_TUMBLING(
                       CURSOR(SELECT STREAM * FROM    "SOURCE_SQL_STREAM_001"),
                       'column1', -- name of column in single quote.
                       10,          -- number of top items.
                       60           -- tumbling window size in seconds 
                       )
                  );
```

The code uses the `TOP_K_ITEMS_TUMBLING` function to find the most frequently traded stock\. Note that, for efficiency, the function approximates the most frequently occurring values\. For more information about the function, see [TOP\_K\_ITEMS\_TUMBLING Function](http://docs.aws.amazon.com/kinesisanalytics/latest/sqlref/top-k.html) in the *Amazon Kinesis Data Analytics SQL Reference*\. 

In the console, this application code is available as a template \(**Approximate Top\-K items**\), which you use to quickly create the application\. You need to update this template code by replacing '*column1*' with `'TICKER_SYMBOL'` to estimate the most frequently occurring values, in a one\-minute tumbling window\.

You can use the following procedure to test this template using the demo stream\.

**To create an application**

1. Complete the Getting Started exercise\. For instructions, see [ Step 3: Create Your Starter Amazon Kinesis Data Analytics Application](get-started-exercise.md)\.

1. Replace the application code in the SQL editor with the **Approximate Top\-K items** template as follows in the SQL editor: 

   1. Delete the existing sample code\.

   1. Choose **Add SQL from templates** and then select the **TopKItems** template\. 

   1. Update the template code by replacing the column name from `COLUMN1` to `'TICKER_SYMBOL'` \(with single quotes around\)\. Also, change the number of items from 10 to 3, so that you get the top three most frequently traded stocks in each one\-minute window\. 

1. Save and run SQL\. Review results in the **Real\-time analytics** tab in the SQL editor\.

   Because the window size is one minute, you need to wait to see the results\. The `DESTINATION_SQL_STREAM` displays three columns \(`ROWTIME`, `ITEM`, and `ITEM_COUNT`\)\. The query emits results every one minute\.