# Step 3: Configure Application Output<a name="app-anomaly-create-ka-app-config-destination"></a>

At this time, you have application code reading heart rate data from a streaming source and assigning an anomaly score to each\. You can now send the application result from the in\-application stream to an external destination, another Kinesis stream \(`OutputStreamTestingAnomalyScores`\)\. You can then analyze the anomaly scores and determine which heart rate is anomalous\. You can extend this application further to generate alerts\. Follow these steps to configure application output:

1. In the SQL editor, choose either **Destination** or **Add a destination** in the application dashboard\. 

1. On the **Add a destination** page, choose **Select from your streams**, and then choose the `OutputStreamTestingAnomalyScores` stream you created in the preceding section\.

   Now you have an external destination, where Amazon Kinesis Data Analytics persists any records your application writes to the in\-application stream `DESTINATION_SQL_STREAM`\. 

1. You can optionally configure AWS Lambda to monitor the `OutputStreamTestingAnomalyScores` stream and send you alerts\. For instructions, see [Using the Lambda Preprocessing Feature](lambda-preprocessing.md)\. If not, you can review the records that Amazon Kinesis Data Analytics writes to the external destination, the Kinesis stream `OutputStreamTestingAnomalyScores`, as described in the next step\.

**Next Step**  
[Step 4: Verify Output](app-anomaly-verify-output.md)