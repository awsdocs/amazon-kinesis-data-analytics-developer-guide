# Step 1: Create the Input and Output Streams<a name="app-hotspots-prepare"></a>

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
    
   import boto3
   import json
   import time
   
   from random import random
   
   # Modify this section to reflect your AWS configuration.
   awsRegion = ""         # The AWS region where your Kinesis Analytics application is configured.
   accessKeyId = ""       # Your AWS Access Key ID
   secretAccessKey = ""   # Your AWS Secret Access Key
   inputStream = "ExampleInputStream"       # The name of the stream being used as input into the Kinesis Analytics hotspots application
   
   # Variables that control properties of the generated data.
   xRange = [0, 10]       # The range of values taken by the x-coordinate
   yRange = [0, 10]       # The range of values taken by the y-coordinate
   hotspotSideLength = 1  # The side length of the hotspot
   hotspotWeight = 0.2    # The fraction ofpoints that are draw from the hotspots
   
   
   def generate_point_in_rectangle(x_min, width, y_min, height):
       """Generate points uniformly in the given rectangle."""
       return {
           'x': x_min + random() * width,
           'y': y_min + random() * height
       }
   
   
   class RecordGenerator(object):
       """A class used to generate points used as input to the hotspot detection algorithm. With probability hotspotWeight,
       a point is drawn from a hotspot, otherwise it is drawn from the base distribution. The location of the hotspot
       changes after every 1000 points generated."""
   
       def __init__(self):
           self.x_min = xRange[0]
           self.width = xRange[1] - xRange[0]
           self.y_min = yRange[0]
           self.height = yRange[1] - yRange[0]
           self.points_generated = 0
           self.hotspot_x_min = None
           self.hotspot_y_min = None
   
       def get_record(self):
           if self.points_generated % 1000 == 0:
               self.update_hotspot()
   
           if random() < hotspotWeight:
               record = generate_point_in_rectangle(self.hotspot_x_min, hotspotSideLength, self.hotspot_y_min,
                                                    hotspotSideLength)
               record['is_hot'] = 'Y'
           else:
               record = generate_point_in_rectangle(self.x_min, self.width, self.y_min, self.height)
               record['is_hot'] = 'N'
   
           self.points_generated += 1
           data = json.dumps(record)
           return {'Data': bytes(data, 'utf-8'), 'PartitionKey': 'partition_key'}
   
       def get_records(self, n):
           return [self.get_record() for _ in range(n)]
   
       def update_hotspot(self):
           self.hotspot_x_min = self.x_min + random() * (self.width - hotspotSideLength)
           self.hotspot_y_min = self.y_min + random() * (self.height - hotspotSideLength)
   
   
   def main():
       kinesis = boto3.client('kinesis')
   
       generator = RecordGenerator()
       batch_size = 10
   
       while True:
           records = generator.get_records(batch_size)
           print(records)
           kinesis.put_records(StreamName="ExampleInputStream", Records=records)
   
           time.sleep(0.1)
   
   
   if __name__ == "__main__":
       main()
   ```

**Next Step**  
[Step 2: Create the Kinesis Data Analytics Application](app-hotspot-create-app.md)