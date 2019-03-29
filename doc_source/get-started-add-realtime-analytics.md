# Step 3\.3: Add Real\-Time Analytics \(Add Application Code\)<a name="get-started-add-realtime-analytics"></a>

You can write your own SQL queries against the in\-application stream, but for the following step you use one of the templates that provides sample code\.

1. On the application hub page, choose **Go to SQL editor**\.   
![\[Screenshot of the example application page with Go to SQL editor button.\]](http://docs.aws.amazon.com/kinesisanalytics/latest/dev/images/gs-v2-40.png)

1. In the **Would you like to start running "ExampleApp"?** dialog box, choose **Yes, start application**\.

   The console sends a request to start the application \(see [StartApplication](API_StartApplication.md)\), and then the SQL editor page appears\.

1. The console opens the SQL editor page\. Review the page, including the buttons \(**Add SQL from templates**, **Save and run SQL**\) and various tabs\.

1. In the SQL editor, choose **Add SQL from templates**\.

1. From the available template list, choose **Continuous filter**\. The sample code reads data from one in\-application stream \(the `WHERE` clause filters the rows\) and inserts it in another in\-application stream as follows:
   + It creates the in\-application stream `DESTINATION_SQL_STREAM`\.
   + It creates a pump `STREAM_PUMP`, and uses it to select rows from `SOURCE_SQL_STREAM_001` and insert them in the `DESTINATION_SQL_STREAM`\. 

1. Choose **Add this SQL to editor**\. 

1. Test the application code as follows:

   Remember, you already started the application \(status is RUNNING\)\. Therefore, Amazon Kinesis Data Analytics is already continuously reading from the streaming source and adding rows to the in\-application stream `SOURCE_SQL_STREAM_001`\.

   1. In the SQL Editor, choose **Save and run SQL**\. The console first sends update request to save the application code\. Then, the code continuously executes\.

   1. You can see the results in the **Real\-time analytics** tab\.   
![\[Screenshot of the SQL editor with results shown in the real-time analytics tab.\]](http://docs.aws.amazon.com/kinesisanalytics/latest/dev/images/gs-v2-50.png)

      The SQL editor has the following tabs:
      + The **Source data** tab shows an in\-application input stream that is mapped to the streaming source\. Choose the in\-application stream, and you can see data coming in\. Note the additional columns in the in\-application input stream that weren't specified in the input configuration\. These include the following timestamp columns:

         
        + **ROWTIME** – Each row in an in\-application stream has a special column called `ROWTIME`\. This column is the timestamp when Amazon Kinesis Data Analytics inserted the row in the first in\-application stream \(the in\-application input stream that is mapped to the streaming source\)\.

           
        + **Approximate\_Arrival\_Time** – Each Kinesis Data Analytics record includes a value called `Approximate_Arrival_Time`\. This value is the approximate arrival timestamp that is set when the streaming source successfully receives and stores the record\. When Kinesis Data Analytics reads records from a streaming source, it fetches this column into the in\-application input stream\. 

        These timestamp values are useful in windowed queries that are time\-based\. For more information, see [Windowed Queries](windowed-sql.md)\.

         
      + The **Real\-time analytics** tab shows all the other in\-application streams created by your application code\. It also includes the error stream\. Kinesis Data Analytics sends any rows it cannot process to the error stream\. For more information, see [Error Handling](error-handling.md)\.

         

        Choose `DESTINATION_SQL_STREAM` to view the rows your application code inserted\. Note the additional columns that your application code didn't create\. These columns include the `ROWTIME` timestamp column\. Kinesis Data Analytics simply copies these values from the source \(`SOURCE_SQL_STREAM_001`\)\.

         
      + The **Destination** tab shows the external destination where Kinesis Data Analytics writes the query results\. You haven't configured any external destination for your application output yet\.

**Next Step**  
[Step 3\.4: \(Optional\) Update the Application Code](get-started-update-appcode.md)