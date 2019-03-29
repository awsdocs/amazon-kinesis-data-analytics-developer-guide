# Step 1: Prepare the Data<a name="app-anomaly-with-ex-prepare"></a>

Before you create an Amazon Kinesis Data Analytics application for this [example](app-anomaly-detection-with-explanation.md), you create a Kinesis data stream to use as the streaming source for your application\. You also run Python code to write simulated blood pressure data to the stream\. 

**Topics**
+ [Step 1\.1: Create a Kinesis Data Stream](#app-anomaly-create-two-streams)
+ [Step 1\.2: Write Sample Records to the Input Stream](#app-anomaly-write-sample-records-inputstream)

## Step 1\.1: Create a Kinesis Data Stream<a name="app-anomaly-create-two-streams"></a>

In this section, you create a Kinesis data stream named `ExampleInputStream`\. You can create this data stream using the AWS Management Console or the AWS CLI\.
+ To use the console:

  1. Sign in to the AWS Management Console and open the Kinesis console at [https://console\.aws\.amazon\.com/kinesis](https://console.aws.amazon.com/kinesis)\.

  1. Choose **Data Streams** in the navigation pane\. Then choose **Create Kinesis stream**\.

  1. For the name, type **ExampleInputStream**\. For the number of shards, type **1**\.
+ Alternatively, to use the AWS CLI to create the data stream, run the following command:

  ```
  $ aws kinesis create-stream --stream-name ExampleInputStream --shard-count 1
  ```

## Step 1\.2: Write Sample Records to the Input Stream<a name="app-anomaly-write-sample-records-inputstream"></a>

In this step, you run Python code to continuously generate sample records and write them to the data stream that you created\. 

1. Install Python and pip\.

   For information about installing Python, see [Python](https://www.python.org/)\. 

   You can install dependencies using pip\. For information about installing pip, see [Installation](https://pip.pypa.io/en/stable/installing/) in the pip documentation\.

1. Run the following Python code\. You can change the Region to the one you want to use for this example\. The `put-record` command in the code writes the JSON records to the stream\.

   ```
    
   import json
   import boto3
   import random
   
   kinesis = boto3.client('kinesis')
   
   # Generate normal blood pressure with a 0.995 probability
   def getNormalBloodPressure():
       data = {}
       data['Systolic'] = random.randint(90, 120)
       data['Diastolic'] = random.randint(60, 80)
       data['BloodPressureLevel'] = 'NORMAL'
       return data
       
   # Generate high blood pressure with probability 0.005
   def getHighBloodPressure():
       data = {}
       data['Systolic'] = random.randint(130, 200)
       data['Diastolic'] = random.randint(90, 150)
       data['BloodPressureLevel'] = 'HIGH'
       return data
       
   # Generate low blood pressure with probability 0.005
   def getLowBloodPressure():
       data = {}
       data['Systolic'] = random.randint(50, 80)
       data['Diastolic'] = random.randint(30, 50)
       data['BloodPressureLevel'] = 'LOW'
       return data
   
   while True:
       rnd = random.random()
       if (rnd < 0.005):
           data = json.dumps(getLowBloodPressure())
           print(data)
           kinesis.put_record(
                   StreamName="BloodPressureExampleInputStream",
                   Data=data,
                   PartitionKey="partitionkey")
       elif (rnd > 0.995):
           data = json.dumps(getHighBloodPressure())
           print(data)
           kinesis.put_record(
                   StreamName="BloodPressureExampleInputStream",
                   Data=data,
                   PartitionKey="partitionkey")
       else:
           data = json.dumps(getNormalBloodPressure())
           print(data)
           kinesis.put_record(
                   StreamName="BloodPressureExampleInputStream",
                   Data=data,
                   PartitionKey="partitionkey")
   ```

**Next Step**  
[Step 2: Create an Analytics Application](app-anom-with-exp-create-app.md)