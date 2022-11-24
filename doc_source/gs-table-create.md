# Create and Run a Kinesis Data Analytics for Apache Flink Application<a name="gs-table-create"></a>

In this exercise, you create a Kinesis Data Analytics application with an Amazon MSK topic as a source and an Amazon S3 bucket as a sink\.

**Topics**
+ [Create Dependent Resources](#gs-table-resources)
+ [Write Sample Records to the Input Stream](#gs-table-2)
+ [Download and Examine the Apache Flink Streaming Java Code](#gs-table-5)
+ [Compile the Application Code](#gs-table-5.5)
+ [Upload the Apache Flink Streaming Java Code](#gs-table-6)
+ [Create and Run the Kinesis Data Analytics Application](#gs-table-7)
+ [Next Step](#gs-table-next-step-4)

## Create Dependent Resources<a name="gs-table-resources"></a>

Before you create an Amazon Kinesis Data Analytics for Apache Flink for this exercise, you create the following dependent resources: 
+ A virtual private cloud \(VPC\) based on Amazon VPC and an Amazon MSK cluster
+ An Amazon S3 bucket to store the application's code and output \(`ka-app-code-<username>`\) 

### Create a VPC and an Amazon MSK Cluster<a name="gs-table-resources-msk"></a>

To create a VPC and Amazon MSK cluster to access from your Kinesis Data Analytics application, follow the [Getting Started Using Amazon MSK](https://docs.aws.amazon.com/msk/latest/developerguide/gs-table.html) tutorial\.

When completing the tutorial, note the following:
+ Record the bootstrap server list for your cluster\. You can get the list of bootstrap servers with the following command, replacing *`ClusterArn`* with the Amazon Resource Name \(ARN\) of your MSK cluster:

  ```
  aws kafka get-bootstrap-brokers --region us-west-2 --cluster-arn ClusterArn
  {...
      "BootstrapBrokerStringTls": "b-2.awskafkatutorialcluste.t79r6y.c4.kafka.us-west-2.amazonaws.com:9094,b-1.awskafkatutorialcluste.t79r6y.c4.kafka.us-west-2.amazonaws.com:9094,b-3.awskafkatutorialcluste.t79r6y.c4.kafka.us-west-2.amazonaws.com:9094"
  }
  ```
+ When following the steps in the tutorials, be sure to use your selected AWS Region in your code, commands, and console entries\.

### Create an Amazon S3 Bucket<a name="gs-table-resources-s3"></a>

You can create the Amazon S3 bucket using the console\. For instructions for creating this resource, see the following topics:
+ [How Do I Create an S3 Bucket?](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/create-bucket.html) in the *Amazon Simple Storage Service User Guide*\. Give the Amazon S3 bucket a globally unique name by appending your login name, such as **ka\-app\-code\-*<username>***\. 

### Other Resources<a name="gs-table-resources-cw"></a>

When you create your application, Kinesis Data Analytics creates the following Amazon CloudWatch resources if they don't already exist:
+ A log group called `/aws/kinesis-analytics-java/MyApplication`\.
+ A log stream called `kinesis-analytics-log-stream`\.

## Write Sample Records to the Input Stream<a name="gs-table-2"></a>

In this section, you use a Python script to write sample records to the Amazon MSK topic for the application to process\.

1. Connect to the client instance you created in [Step 4: Create a Client Machine](https://docs.aws.amazon.com/msk/latest/developerguide/create-client-machine.html) of the [Getting Started Using Amazon MSK](https://docs.aws.amazon.com/msk/latest/developerguide/gs-table.html) tutorial\.

1. Install Python3, Pip, and the Kafka Python library:

   ```
   $ sudo yum install python37
   $ curl -O https://bootstrap.pypa.io/get-pip.py
   $ python3 get-pip.py --user
   $ pip install kafka-python
   ```

1. Create a file named `stock.py` with the following contents\. Replace the `BROKERS` value with your bootstrap broker list you recorded previously\.

   ```
   import datetime
       import json
       import random
       import boto3
   
       STREAM_NAME = "ExampleInputStream"
   
   
       def get_data():
           return {
               'event_time': datetime.datetime.now().isoformat(),
               'ticker': random.choice(['AAPL', 'AMZN', 'MSFT', 'INTC', 'TBV']),
               'price': round(random.random() * 100, 2)}
   
   
       def generate(stream_name, kinesis_client):
           while True:
               data = get_data()
               print(data)
               kinesis_client.put_record(
                   StreamName=stream_name,
                   Data=json.dumps(data),
                   PartitionKey="partitionkey")
   
   
       if __name__ == '__main__':
           generate(STREAM_NAME, boto3.client('kinesis', region_name='us-west-2'))
   ```

1. Later in the tutorial, you run the `stock.py` script to send data to the application\. 

   ```
   $ python3 stock.py
   ```

## Download and Examine the Apache Flink Streaming Java Code<a name="gs-table-5"></a>

The Java application code for this example is available from GitHub\.

**To download the Java application code**

1. Clone the remote repository using the following command:

   ```
   git clone https://github.com/aws-samples/amazon-kinesis-data-analytics-java-examples
   ```

1. Navigate to the `amazon-kinesis-data-analytics-java-examples/GettingStartedTable` directory\.

Note the following about the application code:
+ A [Project Object Model \(pom\.xml\)](https://maven.apache.org/guides/introduction/introduction-to-the-pom.html) file contains information about the application's configuration and dependencies, including the Kinesis Data Analytics libraries\.
+ The `StreamingJob.java` file contains the `main` method that defines the application's functionality\.
+ The application uses a `FlinkKafkaConsumer` to read from the Amazon MSK topic\. The following snippet creates a `FlinkKafkaConsumer` object:

  ```
  final FlinkKafkaConsumer<StockRecord> consumer = new FlinkKafkaConsumer<StockRecord>(kafkaTopic, new KafkaEventDeserializationSchema(), kafkaProps);
  ```
+ Your application creates source and sink connectors to access external resources using `StreamExecutionEnvironment` and `TableEnvironment` objects\. 
+ The application creates source and sink connectors using dynamic application properties, so you can specify your application parameters \(such as your S3 bucket\) without recompiling the code\. 

  ```
  //read the parameters from the Kinesis Analytics environment
  Map<String, Properties> applicationProperties = KinesisAnalyticsRuntime.getApplicationProperties();
  Properties flinkProperties = null;
  
  String kafkaTopic = parameter.get("kafka-topic", "AWSKafkaTutorialTopic");
  String brokers = parameter.get("brokers", "");
  String s3Path = parameter.get("s3Path", "");
  
  if (applicationProperties != null) {
      flinkProperties = applicationProperties.get("FlinkApplicationProperties");
  }
  
  if (flinkProperties != null) {
      kafkaTopic = flinkProperties.get("kafka-topic").toString();
      brokers = flinkProperties.get("brokers").toString();
      s3Path = flinkProperties.get("s3Path").toString();
  }
  ```

  For more information about runtime properties, see [Runtime Properties](how-properties.md)\.

## Compile the Application Code<a name="gs-table-5.5"></a>

In this section, you use the Apache Maven compiler to create the Java code for the application\. For information about installing Apache Maven and the Java Development Kit \(JDK\), see [Prerequisites for Completing the Exercises](getting-started.md#setting-up-prerequisites)\.

**To compile the application code**

1. To use your application code, you compile and package it into a JAR file\. You can compile and package your code in one of two ways:
   + Use the command\-line Maven tool\. Create your JAR file by running the following command in the directory that contains the `pom.xml` file:

     ```
     mvn package -Dflink.version=1.15.2
     ```
   + Use your development environment\. See your development environment documentation for details\.
**Note**  
The provided source code relies on libraries from Java 11\. 

   You can either upload your package as a JAR file, or you can compress your package and upload it as a ZIP file\. If you create your application using the AWS CLI, you specify your code content type \(JAR or ZIP\)\.

1. If there are errors while compiling, verify that your `JAVA_HOME` environment variable is correctly set\.

If the application compiles successfully, the following file is created:

`target/aws-kinesis-analytics-java-apps-1.0.jar`

## Upload the Apache Flink Streaming Java Code<a name="gs-table-6"></a>

In this section, you create an Amazon S3 bucket and upload your application code\.

**To upload the application code**

1. Open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. Choose **Create bucket**\.

1. Enter **ka\-app\-code\-*<username>*** in the **Bucket name** field\. Add a suffix to the bucket name, such as your user name, to make it globally unique\. Choose **Next**\.

1. In the **Configure options** step, keep the settings as they are, and choose **Next**\.

1. In the **Set permissions** step, keep the settings as they are, and choose **Next**\.

1. Choose **Create bucket**\.

1. In the Amazon S3 console, choose the **ka\-app\-code\-*<username>*** bucket, and choose **Upload**\.

1. In the **Select files** step, choose **Add files**\. Navigate to the `aws-kinesis-analytics-java-apps-1.0.jar` file that you created in the previous step\. Choose **Next**\.

1. You don't need to change any of the settings for the object, so choose **Upload**\.

Your application code is now stored in an Amazon S3 bucket where your application can access it\.

## Create and Run the Kinesis Data Analytics Application<a name="gs-table-7"></a>

Follow these steps to create, configure, update, and run the application using the console\.

### Create the Application<a name="gs-table-7-console-create"></a>

1. Open the Kinesis Data Analytics console at [ https://console\.aws\.amazon\.com/kinesisanalytics](https://console.aws.amazon.com/kinesisanalytics)\.

1. On the Kinesis Data Analytics dashboard, choose **Create analytics application**\.

1. On the **Kinesis Analytics \- Create application** page, provide the application details as follows:
   + For **Application name**, enter **MyApplication**\.
   + For **Description**, enter **My java test app**\.
   + For **Runtime**, choose **Apache Flink**\.
   + Leave the version as **Apache Flink version 1\.15\.2 \(Recommended version\)**\.

1. For **Access permissions**, choose **Create / update IAM role `kinesis-analytics-MyApplication-us-west-2`**\.

1. Choose **Create application**\.

**Note**  
When you create a Kinesis Data Analytics application using the console, you have the option of having an IAM role and policy created for your application\. Your application uses this role and policy to access its dependent resources\. These IAM resources are named using your application name and Region as follows:  
Policy: `kinesis-analytics-service-MyApplication-us-west-2`
Role: `kinesis-analytics-MyApplication-us-west-2`

### Edit the IAM Policy<a name="gs-table-7-console-iam"></a>

Edit the IAM policy to add permissions to access the Amazon S3 bucket\.

**To edit the IAM policy to add S3 bucket permissions**

1. Open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. Choose **Policies**\. Choose the **`kinesis-analytics-service-MyApplication-us-west-2`** policy that the console created for you in the previous section\. 

1. On the **Summary** page, choose **Edit policy**\. Choose the **JSON** tab\.

1. Add the highlighted section of the following policy example to the policy\. 

   ```
   {
       "Version": "2012-10-17",
       "Statement": [
           {
           "Sid": "S3",
           "Effect": "Allow",
           "Action": [
               "s3:Abort*",
               "s3:DeleteObject*",
               "s3:GetObject*",
               "s3:GetBucket*",
               "s3:List*",
               "s3:ListBucket",
               "s3:PutObject"
           ],
           "Resource": [
               "arn:aws:s3:::ka-app-code-<username>",
               "arn:aws:s3:::ka-app-code-<username>/*"
           ]
           },
           {
               "Sid": "DescribeLogGroups",
               "Effect": "Allow",
               "Action": [
                   "logs:DescribeLogGroups"
               ],
               "Resource": [
                   "arn:aws:logs:us-west-2:012345678901:log-group:*"
               ]
           },
           {
               "Sid": "DescribeLogStreams",
               "Effect": "Allow",
               "Action": [
                   "logs:DescribeLogStreams"
               ],
               "Resource": [
                   "arn:aws:logs:us-west-2:012345678901:log-group:/aws/kinesis-analytics/MyApplication:log-stream:*"
               ]
           },
           {
               "Sid": "PutLogEvents",
               "Effect": "Allow",
               "Action": [
                   "logs:PutLogEvents"
               ],
               "Resource": [
                   "arn:aws:logs:us-west-2:012345678901:log-group:/aws/kinesis-analytics/MyApplication:log-stream:kinesis-analytics-log-stream"
               ]
           }
        ]
   }
   ```

### Configure the Application<a name="gs-table-7-console-configure"></a>

Use the following procedure to configure the application\.

**To configure the application**

1. On the **MyApplication** page, choose **Configure**\.

1. On the **Configure application** page, provide the **Code location**:
   + For **Amazon S3 bucket**, enter **ka\-app\-code\-*<username>***\.
   + For **Path to Amazon S3 object**, enter **aws\-kinesis\-analytics\-java\-apps\-1\.0\.jar**\.

1. Under **Access to application resources**, for **Access permissions**, choose **Create / update IAM role `kinesis-analytics-MyApplication-us-west-2`**\.

1. Under **Properties**, choose **Create group**\. 

1. Enter the following:    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/kinesisanalytics/latest/java/gs-table-create.html)

1. Under **Monitoring**, ensure that the **Monitoring metrics level** is set to **Application**\.

1. For **CloudWatch logging**, select the **Enable** check box\.

1. In the **Virtual Private Cloud \(VPC\)** section, choose **VPC configuration based on Amazon MSK cluster**\. Choose **AWSKafkaTutorialCluster**\. 

1. Choose **Update**\.

**Note**  
When you choose to enable Amazon CloudWatch logging, Kinesis Data Analytics creates a log group and log stream for you\. The names of these resources are as follows:   
Log group: `/aws/kinesis-analytics/MyApplication`
Log stream: `kinesis-analytics-log-stream`

### Run the Application<a name="gs-table-7-console-run"></a>

Use the following procedure to run the application\.

**To run the application**

1. On the **MyApplication** page, choose **Run**\. Confirm the action\.

1. When the application is running, refresh the page\. The console shows the **Application graph**\.

1. From your Amazon EC2 client, run the Python script you created previously to write records to the Amazon MSK cluster for your application to process:

   ```
   $ python3 stock.py
   ```

### Stop the Application<a name="gs-table-7-console-stop"></a>

To stop the application, on the **MyApplication** page, choose **Stop**\. Confirm the action\.

## Next Step<a name="gs-table-next-step-4"></a>

[Clean Up AWS Resources](gs-table-cleanup.md)