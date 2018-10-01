# Example: Detecting Data Anomalies and Getting an Explanation \(RANDOM\_CUT\_FOREST\_WITH\_EXPLANATION Function\)<a name="app-anomaly-detection-with-explanation"></a>

Amazon Kinesis Data Analytics provides the `RANDOM_CUT_FOREST_WITH_EXPLANATION` function, which assigns an anomaly score to each record based on values in the numeric columns\. The function also provides an explanation of the anomaly\. For more information, see [RANDOM\_CUT\_FOREST\_WITH\_EXPLANATION](https://docs.aws.amazon.com/kinesisanalytics/latest/sqlref/sqlrf-random-cut-forest-with-explanation.html) in the *Amazon Kinesis Data Analytics SQL Reference*\. 

In this exercise, you write application code to obtain anomaly scores for records in your application's streaming source\. You also obtain an explanation for each anomaly\.

**Topics**
+ [Step 1: Prepare the Data](app-anomaly-with-ex-prepare.md)
+ [Step 2: Create an Analytics Application](app-anom-with-exp-create-app.md)
+ [Step 3: Examine the Results](examine-results-with-exp.md)

**First Step**  
[Step 1: Prepare the Data](app-anomaly-with-ex-prepare.md)