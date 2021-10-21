# Tutorial: Using a Custom Keystore with Amazon MSK<a name="example-keystore"></a>

The following tutorial demonstrates how to access an Amazon MSK cluster that uses a custom keystore for encryption in transit\.

You can also use the technique in this tutorial for interactions between a Kinesis Data Analytics application and other Apache Kafka sources, such as the following:
+ A custom Apache Kafka cluster hosted in AWS \([Amazon EC2](https://aws.amazon.com/ec2/) or [Amazon EKS](https://aws.amazon.com/eks/)\)
+ A [Confluent Kafka](https://www.confluent.io) cluster hosted in AWS
+ An on\-premises Kafka cluster accessed through [AWS Direct Connect](https://aws.amazon.com/directconnect/) or VPN

Your application will use a custom consumer \(`CustomFlinkKafkaConsumer`\) that overrides the `open` method to load the custom keystore\. This makes the keystore available to the application after the application restarts or replaces threads\. 

The custom keystore is retrieved and stored using the following code, from the `CustomFlinkKafkaConsumer.java` file:

```
@Override
public void open(Configuration configuration) throws Exception {
    // write keystore to /tmp
    // NOTE: make sure that keystore is in JKS format for KDA/Flink. See README for details
    dropFile("/tmp");

    super.open(configuration);
}

private void dropFile(String destFolder) throws Exception
{
    InputStream input = null;
    OutputStream outStream = null;

    try {
        ClassLoader classLoader = Thread.currentThread().getContextClassLoader();
        input = classLoader.getResourceAsStream("kafka.client.truststore.jks");
        byte[] buffer = new byte[input.available()];
        input.read(buffer);

        File destDir = new File(destFolder);
        File targetFile = new File(destDir, "kafka.client.truststore.jks");
        outStream = new FileOutputStream(targetFile);
        outStream.write(buffer);
        outStream.flush();
    }
```

**Note**  
Apache Flink requires the keystore to be in [JKS format](https://en.wikipedia.org/wiki/Java_KeyStore)\.

**Note**  
To set up the required prerequisites for this exercise, first complete the [Getting Started \(DataStream API\)](getting-started.md) exercise\. 

**Topics**
+ [Create a VPC with an Amazon MSK Cluster](#example-keystore-createcluster)
+ [Create a Custom Keystore and Apply It to Your Cluster](#example-keystore-cert)
+ [Create the Application Code](#example-keystore-code)
+ [Upload the Apache Flink Streaming Java Code](#example-keystore-upload)
+ [Create the Application](#example-keystore-create)
+ [Configure the Application](#example-keystore-configure)
+ [Run the Application](#example-keystore-run)
+ [Test the Application](#example-keystore-test)

## Create a VPC with an Amazon MSK Cluster<a name="example-keystore-createcluster"></a>

To create a sample VPC and Amazon MSK cluster to access from a Kinesis Data Analytics application, follow the [Getting Started Using Amazon MSK](https://docs.aws.amazon.com/msk/latest/developerguide/getting-started.html) tutorial\.

When completing the tutorial, also do the following:
+ In [Step 5: Create a Topic](https://docs.aws.amazon.com/msk/latest/developerguide/create-topic.html), repeat the `kafka-topics.sh --create` command to create a destination topic named `AWSKafkaTutorialTopicDestination`:

  ```
  bin/kafka-topics.sh --create --zookeeper ZooKeeperConnectionString --replication-factor 3 --partitions 1 --topic AWSKafkaTutorialTopicDestination
  ```
**Note**  
If the `kafka-topics.sh` command returns a `ZooKeeperClientTimeoutException`, verify that the Kafka cluster's security group has an inbound rule to allow all traffic from the client instance's private IP address\.
+ Record the bootstrap server list for your cluster\. You can get the list of bootstrap servers with the following command \(replace *ClusterArn* with the ARN of your MSK cluster\):

  ```
  aws kafka get-bootstrap-brokers --region us-west-2 --cluster-arn ClusterArn
  {...
      "BootstrapBrokerStringTls": "b-2.awskafkatutorialcluste.t79r6y.c4.kafka.us-west-2.amazonaws.com:9094,b-1.awskafkatutorialcluste.t79r6y.c4.kafka.us-west-2.amazonaws.com:9094,b-3.awskafkatutorialcluste.t79r6y.c4.kafka.us-west-2.amazonaws.com:9094"
  }
  ```
+ When following the steps in this tutorial and the prerequisite tutorials, be sure to use your selected AWS Region in your code, commands, and console entries\.

## Create a Custom Keystore and Apply It to Your Cluster<a name="example-keystore-cert"></a>

In this section, you create a custom certificate authority \(CA\), use it to generate a custom keystore, and apply it to your MSK cluster\.

To create and apply your custom keystore, follow the [Client Authentication](https://docs.aws.amazon.com/msk/latest/developerguide/msk-authentication.html) tutorial in the *Amazon Managed Streaming for Apache Kafka Developer Guide*\.

## Create the Application Code<a name="example-keystore-code"></a>

In this section, you download and compile the application JAR file\.

The Java application code for this example is available from GitHub\. To download the application code, do the following:

1. Install the Git client if you haven't already\. For more information, see [Installing Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)\. 

1. Clone the remote repository with the following command:

   ```
   git clone https://github.com/aws-samples/amazon-kinesis-data-analytics-java-examples
   ```

1. The application code is located in the `amazon-kinesis-data-analytics-java-examples/CustomKeystore/KDAFlinkStreamingJob.java` and `CustomFlinkKafkaConsumer.java` files\. You can examine the code to familiarize yourself with the structure of Kinesis Data Analytics for Apache Flink application code\.

1. Use either the command line Maven tool or your preferred development environment to create the JAR file\. To compile the JAR file using the command line Maven tool, enter the following:

   ```
   mvn package -Dflink.version=1.13.2
   ```

   If the build is successful, the following file is created:

   ```
   target/flink-app-1.0-SNAPSHOT.jar
   ```
**Note**  
The provided source code relies on libraries from Java 11\. If you're using a development environment, 

## Upload the Apache Flink Streaming Java Code<a name="example-keystore-upload"></a>

In this section, you upload your application code to the Amazon S3 bucket that you created in the [Getting Started \(DataStream API\)](getting-started.md) tutorial\.

**Note**  
If you deleted the Amazon S3 bucket from the Getting Started tutorial, follow the [Upload the Apache Flink Streaming Java Code](get-started-exercise.md#get-started-exercise-6) step again\.

1. In the Amazon S3 console, choose the **ka\-app\-code\-*<username>*** bucket, and choose **Upload**\.

1. In the **Select files** step, choose **Add files**\. Navigate to the `KafkaGettingStartedJob-1.0.jar` file that you created in the previous step\. 

1. You don't need to change any of the settings for the object, so choose **Upload**\.

Your application code is now stored in an Amazon S3 bucket where your application can access it\.

## Create the Application<a name="example-keystore-create"></a>

1. Open the Kinesis Data Analytics console at [ https://console\.aws\.amazon\.com/kinesisanalytics](https://console.aws.amazon.com/kinesisanalytics)\.

1. On the Amazon Kinesis Data Analytics dashboard, choose **Create analytics application**\.

1. On the **Kinesis Analytics \- Create application** page, provide the application details as follows:
   + For **Application name**, enter **MyApplication**\.
   + For **Runtime**, choose **Apache Flink version 1\.13\.2**\.

1. For **Access permissions**, choose **Create / update IAM role `kinesis-analytics-MyApplication-us-west-2`**\.

1. Choose **Create application**\.

**Note**  
When you create a Kinesis Data Analytics for Apache Flink application using the console, you have the option of having an IAM role and policy created for your application\. Your application uses this role and policy to access its dependent resources\. These IAM resources are named using your application name and Region as follows:  
Policy: `kinesis-analytics-service-MyApplication-us-west-2`
Role: `kinesis-analytics-MyApplication-us-west-2`

## Configure the Application<a name="example-keystore-configure"></a>

1. On the **MyApplication** page, choose **Configure**\.

1. On the **Configure application** page, provide the **Code location**:
   + For **Amazon S3 bucket**, enter **ka\-app\-code\-*<username>***\.
   + For **Path to Amazon S3 object**, enter **flink\-app\-1\.0\-SNAPSHOT\.jar**\.

1. Under **Access to application resources**, for **Access permissions**, choose **Create / update IAM role `kinesis-analytics-MyApplication-us-west-2`**\.
**Note**  
When you specify application resources using the console \(such as logs or a VPC\), the console modifies your application execution role to grant permission to access those resources\.

1. Under **Properties**, choose **Add Group**\. Create a property group named **KafkaSource** with the following properties:  
****    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/kinesisanalytics/latest/java/example-keystore.html)
**Note**  
The **ssl\.truststore\.password** for the default certificate is "changeit"—you don't need to change this value if you're using the default certificate\.

   Choose **Add Group** again\. Create a property group named **KafkaSink** with the following properties:  
****    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/kinesisanalytics/latest/java/example-keystore.html)

   The application code reads the above application properties to configure the source and sink used to interact with your VPC and Amazon MSK cluster\. For more information about using properties, see [Runtime Properties](how-properties.md)\.

1. Under **Snapshots**, choose **Disable**\. This will make it easier to update the application without loading invalid application state data\.

1. Under **Monitoring**, ensure that the **Monitoring metrics level** is set to **Application**\.

1. For **CloudWatch logging**, choose the **Enable** check box\.

1. In the **Virtual Private Cloud \(VPC\)** section, choose the VPC to associate with your application\. Choose the subnets and security group associated with your VPC that you want the application to use to access VPC resources\.

1. Choose **Update**\.

**Note**  
When you choose to enable CloudWatch logging, Kinesis Data Analytics creates a log group and log stream for you\. The names of these resources are as follows:   
Log group: `/aws/kinesis-analytics/MyApplication`
Log stream: `kinesis-analytics-log-stream`
This log stream is used to monitor the application\.

## Run the Application<a name="example-keystore-run"></a>

1. On the **MyApplication** page, choose **Run**\. Leave the **Run without snapshot** option selected, and confirm the action\.

1. When the application is running, refresh the page\. The console shows the **Application graph**\.

## Test the Application<a name="example-keystore-test"></a>

In this section, you write records to the source topic\. The application reads records from the source topic and writes them to the destination topic\. You verify that the application is working by writing records to the source topic and reading records from the destination topic\.

To write and read records from the topics, follow the steps in [Step 6: Produce and Consume Data](https://docs.aws.amazon.com/msk/latest/developerguide/produce-consume.html) in the [Getting Started Using Amazon MSK](https://docs.aws.amazon.com/msk/latest/developerguide/getting-started.html) tutorial\.

To read from the destination topic, use the destination topic name instead of the source topic in your second connection to the cluster:

```
bin/kafka-console-consumer.sh --bootstrap-server BootstrapBrokerString --consumer.config client.properties --topic AWSKafkaTutorialTopicDestination --from-beginning
```

If no records appear in the destination topic, see the [Cannot Access Resources in a VPC](troubleshooting-symptoms.md#troubleshooting-rt-vpc) section in the [Troubleshooting](troubleshooting.md) topic\.