# Step 3: Configure Application Output<a name="app-anomaly-create-ka-app-config-destination"></a>

After completing [Step 2: Create an Application](app-anom-score-create-app.md), you have application code that is reading heart rate data from a streaming source and assigning an anomaly score to each\. 

You can now send the application results from the in\-application stream to an external destination, which is another Kinesis data stream \(`OutputStreamTestingAnomalyScores`\)\. You can analyze the anomaly scores and determine which heart rate is anomalous\. You can then extend this application further to generate alerts\. 

Follow these steps to configure application output:

1. Open the Amazon Kinesis Data Analytics console\. In the SQL editor, choose either **Destination** or **Add a destination** in the application dashboard\. 

1. On the **Connect to destination** page, choose the `OutputStreamTestingAnomalyScores` stream that you created in the preceding section\.

   Now you have an external destination, where Amazon Kinesis Data Analytics persists any records your application writes to the in\-application stream `DESTINATION_SQL_STREAM`\. 

1. You can optionally configure AWS Lambda to monitor the `OutputStreamTestingAnomalyScores` stream and send you alerts\. For instructions, see [Preprocessing Data Using a Lambda Function](lambda-preprocessing.md)\. If you don't set alerts, you can review the records that Kinesis Data Analytics writes to the external destination, which is the Kinesis data stream `OutputStreamTestingAnomalyScores`, as described in [Step 4: Verify Output](app-anomaly-verify-output.md)\.

**Next Step**  
[Step 4: Verify Output](app-anomaly-verify-output.md)