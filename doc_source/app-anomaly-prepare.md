# Step 1: Prepare<a name="app-anomaly-prepare"></a>

Before you create an Amazon Kinesis Data Analytics application for this exercise, you must create two Kinesis data streams\. Configure one of the streams as the streaming source for your application, and the other stream as the destination where Kinesis Data Analytics persists your application output\. 

**Topics**
+ [Step 1\.1: Create the Input and Output Data Streams](#app-anomaly-create-two-streams)
+ [Step 1\.2: Write Sample Records to the Input Stream](#app-anomaly-write-sample-records-inputstream)

## Step 1\.1: Create the Input and Output Data Streams<a name="app-anomaly-create-two-streams"></a>

In this section, you create two Kinesis streams: `ExampleInputStream` and `ExampleOutputStream`\. You can create these streams using the AWS Management Console or the AWS CLI\.
+ 

**To use the console**

  1. Sign in to the AWS Management Console and open the Kinesis console at [https://console\.aws\.amazon\.com/kinesis](https://console.aws.amazon.com/kinesis)\.

  1. Choose **Create data stream**\. Create a stream with one shard named `ExampleInputStream`\. For more information, see [Create a Stream](https://docs.aws.amazon.com/streams/latest/dev/learning-kinesis-module-one-create-stream.html) in the *Amazon Kinesis Data Streams Developer Guide*\.

  1. Repeat the previous step, creating a stream with one shard named `ExampleOutputStream`\.
+ 

**To use the AWS CLI**

  1. Use the following Kinesis `create-stream` AWS CLI command to create the first stream \(`ExampleInputStream`\)\.

     ```
     $ aws kinesis create-stream \
     --stream-name ExampleInputStream \
     --shard-count 1 \
     --region us-east-1 \
     --profile adminuser
     ```

  1. Run the same command, changing the stream name to `ExampleOutputStream`\. This command creates the second stream that the application uses to write output\.

## Step 1\.2: Write Sample Records to the Input Stream<a name="app-anomaly-write-sample-records-inputstream"></a>

In this step, you run Python code to continuously generate sample records and write these records to the `ExampleInputStream` stream\.

```
{"heartRate": 60, "rateType":"NORMAL"} 
...
{"heartRate": 180, "rateType":"HIGH"}
```

1. Install Python and `pip`\.

   For information about installing Python, see the [Python](https://www.python.org/) website\. 

   You can install dependencies using pip\. For information about installing pip, see [Installation](https://pip.pypa.io/en/stable/installing/) on the pip website\.

1. Run the following Python code\. The `put-record` command in the code writes the JSON records to the stream\.

   ```
    
   import json
   import boto3
   import random
   
   kinesis = boto3.client('kinesis')
   
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
           print(data)
           kinesis.put_record(
                   StreamName="ExampleInputStream",
                   Data=data,
                   PartitionKey="partitionkey")
       else:
           data = json.dumps(getNormalHeartRate())
           print(data)
           kinesis.put_record(
                   StreamName="ExampleInputStream",
                   Data=data,
                   PartitionKey="partitionkey")
   ```

**Next Step**  
[Step 2: Create an Application](app-anom-score-create-app.md)