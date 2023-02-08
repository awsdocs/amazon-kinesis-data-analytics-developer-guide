# Step 1: Prepare the Data<a name="app-anomaly-with-ex-prepare"></a>

**Warning**  
For new projects, we recommend that you use the new Kinesis Data Analytics Studio over Kinesis Data Analytics for SQL Applications\. Kinesis Data Analytics Studio combines ease of use with advanced analytical capabilities, enabling you to build sophisticated stream processing applications in minutes\.

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
    
   from enum import Enum
   import json
   import random
   import boto3
   
   STREAM_NAME = "ExampleInputStream"
   
   
   class PressureType(Enum):
       low = 'LOW'
       normal = 'NORMAL'
       high = 'HIGH'
   
   
   def get_blood_pressure(pressure_type):
       pressure = {'BloodPressureLevel': pressure_type.value}
       if pressure_type == PressureType.low:
           pressure['Systolic'] = random.randint(50, 80)
           pressure['Diastolic'] = random.randint(30, 50)
       elif pressure_type == PressureType.normal:
           pressure['Systolic'] = random.randint(90, 120)
           pressure['Diastolic'] = random.randint(60, 80)
       elif pressure_type == PressureType.high:
           pressure['Systolic'] = random.randint(130, 200)
           pressure['Diastolic'] = random.randint(90, 150)
       else:
           raise TypeError
       return pressure
   
   
   def generate(stream_name, kinesis_client):
       while True:
           rnd = random.random()
           pressure_type = (
               PressureType.low if rnd < 0.005
               else PressureType.high if rnd > 0.995
               else PressureType.normal)
           blood_pressure = get_blood_pressure(pressure_type)
           print(blood_pressure)
           kinesis_client.put_record(
               StreamName=stream_name,
               Data=json.dumps(blood_pressure),
               PartitionKey="partitionkey")
   
   
   if __name__ == '__main__':
       generate(STREAM_NAME, boto3.client('kinesis'))
   ```

**Next Step**  
[Step 2: Create an Analytics Application](app-anom-with-exp-create-app.md)