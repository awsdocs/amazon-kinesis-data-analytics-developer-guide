# Step 1: Prepare the Data<a name="app-anomaly-with-ex-prepare"></a>

Before you create an Amazon Kinesis Data Analytics application for this [example](app-anomaly-detection-with-explanation.md), you create a Kinesis data stream to use as the streaming source for your application\. You also run Python code to write simulated blood pressure data to the stream\. 

## Step 1\.1: Create a Kinesis Data Stream<a name="app-anomaly-create-two-streams"></a>

In this section, you create a Kinesis data stream named `ExampleInputStream`\. You can create this data stream using the AWS Management Console or the AWS CLI\.

+ To use the console:

  1. Sign in to the AWS Management Console and open the Kinesis console at [https://console\.aws\.amazon\.com/kinesis](https://console.aws.amazon.com/kinesis)\.

  1. Go to the [Data Streams dashboard](https://console.aws.amazon.com/kinesis/home?#/streams/list), and choose **Create Kinesis stream**\.

  1. For the name, type **ExampleInputStream**\. For the number of shards, type **1**\.

   ****\.

+ Alternatively, to use the AWS CLI to create the data stream, run the following command:

  ```
  $ aws kinesis create-stream --stream-name ExampleInputStream --shard-count 1
  ```

## Step 1\.2: Write Sample Records to the Input Stream<a name="app-anomaly-write-sample-records-inputstream"></a>

In this step, you run Python code to continuously generate sample records and write them to the data stream you created\. 

1. Install Python and pip\.

   For information about installing Python, see [Python](https://www.python.org/)\. 

   You can install dependencies using pip\. For information about installing pip, see [Installation](https://pip.pypa.io/en/stable/installing/) in the pip documentation\.

1. Run the following Python code\. You can change the Region to the one you want to use for this example\. The `put-record` command in the code writes the JSON records to the stream\.

   ```
   import json
   from boto import kinesis
   import random
   
   kinesis = kinesis.connect_to_region("us-east-1")
   
   # generate normal blood pressure with a 0.995 probability
   def getNormalBloodPressure():
       data = {}
       data['Systolic'] = random.randint(90, 120)
       data['Diastolic'] = random.randint(60, 80)
       data['BloodPressureLevel'] = 'NORMAL'
       return data
       
   # generate high blood pressure with probability 0.005
   def getHighBloodPressure():
       data = {}
       data['Systolic'] = random.randint(130, 200)
       data['Diastolic'] = random.randint(90, 150)
       data['BloodPressureLevel'] = 'HIGH'
       return data
       
   # generate low blood pressure with probability 0.005
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
           kinesis.put_record("BloodPressureExampleInputStream", data, "partitionkey")
       elif (rnd > 0.995):
           data = json.dumps(getHighBloodPressure())
           print(data)
           kinesis.put_record("BloodPressureExampleInputStream", data, "partitionkey")
       else:
           data = json.dumps(getNormalBloodPressure())
           print(data)
           kinesis.put_record("BloodPressureExampleInputStream", data, "partitionkey")
   ```

The previous code writes to `ExampleInputStream` records similar to the following examples:

```
{"Systolic": 109, "Diastolic": 64, "BloodPressureLevel": "NORMAL"}
{"Systolic": 99, "Diastolic": 72, "BloodPressureLevel": "NORMAL"}
{"Systolic": 159, "Diastolic": 100, "BloodPressureLevel": "HIGH"}
{"Systolic": 94, "Diastolic": 75, "BloodPressureLevel": "NORMAL"}
{"Systolic": 91, "Diastolic": 78, "BloodPressureLevel": "NORMAL"}
{"Systolic": 91, "Diastolic": 74, "BloodPressureLevel": "NORMAL"}
{"Systolic": 102, "Diastolic": 75, "BloodPressureLevel": "NORMAL"}
{"Systolic": 50, "Diastolic": 31, "BloodPressureLevel": "LOW"}
{"Systolic": 100, "Diastolic": 66, "BloodPressureLevel": "NORMAL"}
{"Systolic": 115, "Diastolic": 65, "BloodPressureLevel": "NORMAL"}
{"Systolic": 99, "Diastolic": 74, "BloodPressureLevel": "NORMAL"}
```

**Next Step**  
[Step 2: Create an Analytics Application](app-anom-with-exp-create-app.md)