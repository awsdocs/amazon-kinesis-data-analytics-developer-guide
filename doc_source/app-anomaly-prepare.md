# Step 1: Prepare<a name="app-anomaly-prepare"></a>

Before you create an Amazon Kinesis Data Analytics application for this exercise, you create two Kinesis streams\. You configure one of the streams as the streaming source for your application, and another stream as destination where Amazon Kinesis Data Analytics persists your application output\. 

## Step 1\.1: Create Two Kinesis Streams<a name="app-anomaly-create-two-streams"></a>

In this section, you create two Kinesis streams \(`ExampleInputStream` and `ExampleOutputStream`\)\. 

1. You can create these streams using the console or the AWS CLI\.

   + Sign in to the AWS Management Console and open the Kinesis Data Analytics console at [ https://console\.aws\.amazon\.com/kinesisanalytics](https://console.aws.amazon.com/kinesisanalytics)\.

   + Choose **Kinesis Stream**, and then create a stream with one shard\.

   + Use the following Kinesis `create-stream` CLI command to create the first stream \(`ExampleInputStream`\)\.

     ```
     $ aws kinesis create-stream \
     --stream-name ExampleInputStream \
     --shard-count 1 \
     --region us-east-1 \
     --profile adminuser
     ```

1. Run the same command, changing the stream name to `ExampleOutputStream`, to create the second stream that the application will use to write output\.

## Step 1\.2: Write Sample Records to the Input Stream<a name="app-anomaly-write-sample-records-inputstream"></a>

In this step, you run Python code to continuously generate sample records and write to the `ExampleInputStream` stream\.

```
{"heartRate": 60, "rateType":"NORMAL"} 
...
{"heartRate": 180, "rateType":"HIGH"}
```

The code writes these records to the `ExampleInputStream` stream\.

1. Install Python and `pip`\.

   For information about installing Python, see the [Python](https://www.python.org/) website\. 

   You can install dependencies using pip\. For information about installing pip, see [Installation](https://pip.pypa.io/en/stable/installing/) on the pip website\.

1. Run the following Python code\. The `put-record` command in the code writes the JSON records to the stream\.

   ```
   import json
   from boto import kinesis
   import random
   
   kinesis = kinesis.connect_to_region("us-east-1")
   # generate normal heart rate with probability .99
   def getNormalHeartRate():
       data = {}
       data['heartRate'] = random.randint(60, 100)
       data['rateType'] = "NORMAL"
       return data
   # generate high heart rate with probability .01 (very few)
   def getHighHeartRate():
       data = {}
       data['heartRate'] = random.randint(150, 200)
       data['rateType'] = "HIGH"
       return data
   
   while True:
       rnd = random.random()
       if (rnd < 0.01):
           data = json.dumps(getHighHeartRate())
           print data
           kinesis.put_record("ExampleInputStream", data, "partitionkey")
       else:
           data = json.dumps(getNormalHeartRate())
           print data
           kinesis.put_record("ExampleInputStream", data, "partitionkey")
   ```

**Next Step**  
[Step 2: Create an Application](app-anom-score-create-app.md)