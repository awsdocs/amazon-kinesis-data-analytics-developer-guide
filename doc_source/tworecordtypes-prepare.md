# Step 1: Prepare the Data<a name="tworecordtypes-prepare"></a>

In this section, you create a Kinesis data stream, and then populate order and trade records on the stream\. This is your streaming source for the application that you create in the next step\.

**Topics**
+ [Step 1\.1: Create a Streaming Source](#tworecordtypes-prepare-create-stream)
+ [Step 1\.2: Populate the Streaming Source](#tworecordtypes-prepare-populate-stream)

## Step 1\.1: Create a Streaming Source<a name="tworecordtypes-prepare-create-stream"></a>

You can create a Kinesis data stream using the console or the AWS CLI\. The example assumes `OrdersAndTradesStream` as the stream name\. 
+ **Using the console** – Sign in to the AWS Management Console and open the Kinesis console at [https://console\.aws\.amazon\.com/kinesis](https://console.aws.amazon.com/kinesis)\. Choose **Data Streams**, and then create a stream with one shard\. For more information, see [Create a Stream](https://docs.aws.amazon.com/streams/latest/dev/learning-kinesis-module-one-create-stream.html) in the *Amazon Kinesis Data Streams Developer Guide*\.
+ **Using the AWS CLI** – Use the following Kinesis `create-stream` AWS CLI command to create the stream:

  ```
  $ aws kinesis create-stream \
  --stream-name OrdersAndTradesStream \
  --shard-count 1 \
  --region us-east-1 \
  --profile adminuser
  ```

## Step 1\.2: Populate the Streaming Source<a name="tworecordtypes-prepare-populate-stream"></a>

Run the following Python script to populate sample records on the `OrdersAndTradesStream`\. If you created the stream with a different name, update the Python code appropriately\. 

1. Install Python and `pip`\.

   For information about installing Python, see the [Python](https://www.python.org/) website\. 

   You can install dependencies using pip\. For information about installing pip, see [Installation](https://pip.pypa.io/en/stable/installing/) on the pip website\.

1. Run the following Python code\. The `put-record` command in the code writes the JSON records to the stream\.

   ```
    
   
   import json
   import boto3
   import random
   
   kinesis = boto3.client('kinesis')
   
   def getOrderData(orderId, ticker):
       data = {}
       data['RecordType'] = "Order"
       data['Oid'] = orderId
       data['Oticker'] = ticker
       data['Oprice'] = random.randint(500, 10000)
       data['Otype'] = "Sell"
       return data
   
   def getTradeData(orderId, tradeId, ticker, tradePrice):
       data = {}
       data['RecordType'] = "Trade"
       data['Tid'] = tradeId
       data['Toid'] = orderId
       data['Tticker'] = ticker
       data['Tprice'] = tradePrice
       return data
   
   x = 1
   while True:
       #rnd = random.random()
       rnd = random.randint(1,3)
       if rnd == 1:
           ticker = "AAAA"
       elif rnd == 2:
           ticker = "BBBB"
       else:
           ticker = "CCCC"
       data = json.dumps(getOrderData(x, ticker))
       kinesis.put_record(StreamName="OrdersAndTradesStream", Data=data, PartitionKey="partitionkey")
       print(data)
       tId = 1
       for y in range (0, random.randint(0,6)):
           tradeId = tId
           tradePrice = random.randint(0, 3000)
           data2 = json.dumps(getTradeData(x, tradeId, ticker, tradePrice));
           kinesis.put_record(StreamName="OrdersAndTradesStream", Data=data2, PartitionKey="partitionkey")
           print(data2)
           tId+=1
           
       x+=1
   ```

**Next Step**  
 [Step 2: Create the Application](tworecordtypes-create-app.md)