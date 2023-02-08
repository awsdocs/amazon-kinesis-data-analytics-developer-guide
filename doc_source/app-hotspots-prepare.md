# Step 1: Create the Input and Output Streams<a name="app-hotspots-prepare"></a>

**Warning**  
For new projects, we recommend that you use the new Kinesis Data Analytics Studio over Kinesis Data Analytics for SQL Applications\. Kinesis Data Analytics Studio combines ease of use with advanced analytical capabilities, enabling you to build sophisticated stream processing applications in minutes\.

Before you create an Amazon Kinesis Data Analytics application for the [Hotspots example](app-hotspots-detection.md), you create two Kinesis data streams\. Configure one of the streams as the streaming source for your application, and the other stream as the destination where Kinesis Data Analytics persists your application output\. 

**Topics**
+ [Step 1\.1: Create the Kinesis Data Streams](#app-hotspots-create-two-streams)
+ [Step 1\.2: Write Sample Records to the Input Stream](#app-hotspots-write-sample-records-inputstream)

## Step 1\.1: Create the Kinesis Data Streams<a name="app-hotspots-create-two-streams"></a>

In this section, you create two Kinesis data streams: `ExampleInputStream` and `ExampleOutputStream`\. 

Create these data streams using the console or the AWS CLI\.
+ To create the data streams using the console:

  1. Sign in to the AWS Management Console and open the Kinesis console at [https://console\.aws\.amazon\.com/kinesis](https://console.aws.amazon.com/kinesis)\.

  1. Choose **Data Streams** in the navigation pane\.

  1. Choose **Create Kinesis stream**, and create a stream with one shard named `ExampleInputStream`\.

  1. Repeat the previous step, creating a stream with one shard named `ExampleOutputStream`\.
+ To create data streams using the AWS CLI:
  + Create streams \(`ExampleInputStream` and `ExampleOutputStream`\) using the following Kinesis `create-stream` AWS CLI command\. To create the second stream, which the application will use to write output, run the same command, changing the stream name to `ExampleOutputStream`\.

    ```
    $ aws kinesis create-stream \
    --stream-name ExampleInputStream \
    --shard-count 1 \
    --region us-west-2 \
    --profile adminuser
                             
    $ aws kinesis create-stream \
    --stream-name ExampleOutputStream \
    --shard-count 1 \
    --region us-west-2 \
    --profile adminuser
    ```

## Step 1\.2: Write Sample Records to the Input Stream<a name="app-hotspots-write-sample-records-inputstream"></a>

In this step, you run Python code to continuously generate sample records and write to the `ExampleInputStream` stream\.

```
{"x": 7.921782426109737, "y": 8.746265312709893, "is_hot": "N"}
{"x": 0.722248626580026, "y": 4.648868803193405, "is_hot": "Y"}
```

1. Install Python and `pip`\.

   For information about installing Python, see the [Python](https://www.python.org/) website\. 

   You can install dependencies using pip\. For information about installing pip, see [Installation](https://pip.pypa.io/en/stable/installing/) on the pip website\.

1. Run the following Python code\. This code does the following:
   + Generates a potential hotspot somewhere in the \(X, Y\) plane\.
   + Generates a set of 1,000 points for each hotspot\. Of these points, 20 percent are clustered around the hotspot\. The rest are generated randomly within the entire space\.
   + The `put-record` command writes the JSON records to the stream\.
**Important**  
Do not upload this file to a web server because it contains your AWS credentials\.

   ```
    
   import json
   from pprint import pprint
   import random
   import time
   import boto3
   
   STREAM_NAME = "ExampleInputStream"
   
   
   def get_hotspot(field, spot_size):
       hotspot = {
           'left': field['left'] + random.random() * (field['width'] - spot_size),
           'width': spot_size,
           'top': field['top'] + random.random() * (field['height'] - spot_size),
           'height': spot_size
       }
       return hotspot
   
   
   def get_record(field, hotspot, hotspot_weight):
       rectangle = hotspot if random.random() < hotspot_weight else field
       point = {
           'x': rectangle['left'] + random.random() * rectangle['width'],
           'y': rectangle['top'] + random.random() * rectangle['height'],
           'is_hot': 'Y' if rectangle is hotspot else 'N'
       }
       return {'Data': json.dumps(point), 'PartitionKey': 'partition_key'}
   
   
   def generate(
           stream_name, field, hotspot_size, hotspot_weight, batch_size, kinesis_client):
       """
       Generates points used as input to a hotspot detection algorithm.
       With probability hotspot_weight (20%), a point is drawn from the hotspot;
       otherwise, it is drawn from the base field. The location of the hotspot
       changes for every 1000 points generated.
       """
       points_generated = 0
       hotspot = None
       while True:
           if points_generated % 1000 == 0:
               hotspot = get_hotspot(field, hotspot_size)
           records = [
               get_record(field, hotspot, hotspot_weight) for _ in range(batch_size)]
           points_generated += len(records)
           pprint(records)
           kinesis_client.put_records(StreamName=stream_name, Records=records)
   
           time.sleep(0.1)
   
   
   if __name__ == "__main__":
       generate(
           stream_name=STREAM_NAME,
           field={'left': 0, 'width': 10, 'top': 0, 'height': 10},
           hotspot_size=1, hotspot_weight=0.2, batch_size=10,
           kinesis_client=boto3.client('kinesis'))
   ```



**Next Step**  
[Step 2: Create the Kinesis Data Analytics Application](app-hotspot-create-app.md)