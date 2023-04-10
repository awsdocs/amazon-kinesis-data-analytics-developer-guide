# Creating a Studio notebook with Kinesis Data Streams<a name="example-notebook-streams"></a>

This tutorial describes how to create a Studio notebook that uses a Kinesis data stream as a source\.

**Topics**
+ [Setup](#example-notebook-streams-setup)
+ [Create an AWS Glue table](#example-notebook-streams-glue)
+ [Create a Studio notebook with Kinesis Data Streams](#example-notebook-streams-create)
+ [Send data to your Kinesis data stream](#example-notebook-streams-send)
+ [Test your Studio notebook](#example-notebook-streams-test)

## Setup<a name="example-notebook-streams-setup"></a>

Before you create a Studio notebook, create a Kinesis data stream \(`ExampleInputStream`\)\. Your application uses this stream for the application source\.

You can create this stream using either the Amazon Kinesis console or the following AWS CLI command\. For console instructions, see [Creating and Updating Data Streams](https://docs.aws.amazon.com/kinesis/latest/dev/amazon-kinesis-streams.html) in the *Amazon Kinesis Data Streams Developer Guide*\. Name the stream **ExampleInputStream** and set the **Number of open shards** to **1**\.

To create the stream \(`ExampleInputStream`\) using the AWS CLI, use the following Amazon Kinesis `create-stream` AWS CLI command\.

```
$ aws kinesis create-stream \
--stream-name ExampleInputStream \
--shard-count 1 \
--region us-east-1 \
--profile adminuser
```

## Create an AWS Glue table<a name="example-notebook-streams-glue"></a>

Your Studio notebook uses an [AWS Glue](https://docs.aws.amazon.com/glue/latest/dg/what-is-glue.html) database for metadata about your Kinesis Data Streams data source\.

**Note**  
You can either manually create the database first or you can let Kinesis Data Analytics create it for you when you create the notebook\. Similarly, you can either manually create the table as described in this section, or you can use the create table connector code for Kinesis Data Analytics in your notebook within Apache Zeppelin to create your table via a DDL statement\. You can then check in AWS Glue to make sure the table was correctly created\.

**Create a Table**

1. Sign in to the AWS Management Console and open the AWS Glue console at [https://console\.aws\.amazon\.com/glue/](https://console.aws.amazon.com/glue/)\.

1. If you don't already have a AWS Glue database, choose **Databases** from the left navigation bar\. Choose **Add Database**\. In the **Add database** window, enter **default** for **Database name**\. Choose **Create**\.

1. In the left navigation bar, choose **Tables**\. In the **Tables** page, choose **Add tables**, **Add table manually**\.

1. In the **Set up your table's properties** page, enter **stock** for the **Table name**\. Make sure you select the database you created previously\. Choose **Next**\.

1. In the **Add a data store** page, choose **Kinesis**\. For the **Stream name**, enter **ExampleInputStream**\. For **Kinesis source URL**, choose enter **https://kinesis\.us\-east\-1\.amazonaws\.com**\. If you copy and paste the **Kinesis source URL**, be sure to delete any leading or trailing spaces\. Choose **Next**\.

1. In the **Classification** page, choose **JSON**\. Choose **Next**\.

1. In the **Define a Schema** page, choose Add Column to add a column\. Add columns with the following properties:    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/kinesisanalytics/latest/java/example-notebook-streams.html)

   Choose **Next**\.

1. On the next page, verify your settings, and choose **Finish**\.

1. Choose your newly created table from the list of tables\.

1. Choose **Edit table** and add a property with the key `kinesisanalytics.proctime` and the value `proctime`\.

1. Choose **Apply**\.

## Create a Studio notebook with Kinesis Data Streams<a name="example-notebook-streams-create"></a>

Now that you have created the resources your application uses, you create your Studio notebook\. 

**Topics**
+ [Create a Studio notebook using the AWS Management Console](#example-notebook-create-streams-console)
+ [Create a Studio notebook using the AWS CLI](#example-notebook-msk-create-api)

### Create a Studio notebook using the AWS Management Console<a name="example-notebook-create-streams-console"></a>

1. Open the Kinesis Data Analytics console at [ https://console\.aws\.amazon\.com/kinesisanalytics/home?region=us\-east\-1\#/applications/dashboard](https://console.aws.amazon.com/kinesisanalytics/home?region=us-east-1#/applications/dashboard)\. 

1. In the **Kinesis Data Analytics applications** page, choose the **Studio** tab\. Choose **Create Studio notebook**\.
**Note**  
You can also create a Studio notebook from the Amazon MSK or Kinesis Data Streams consoles by selecting your input Amazon MSK cluster or Kinesis data stream, and choosing **Process data in real time**\.

1. In the **Create Studio notebook** page, provide the following information:
   + Enter **MyNotebook** for the name of the notebook\.
   + Choose **default** for **AWS Glue database**\.

   Choose **Create Studio notebook**\.

1. In the **MyNotebook** page, choose **Run**\. Wait for the **Status** to show **Running**\. Charges apply when the notebook is running\.

### Create a Studio notebook using the AWS CLI<a name="example-notebook-msk-create-api"></a>

To create your Studio notebook using the AWS CLI, do the following:

1. Verify your account ID\. You need this value to create your application\.

1. Create the role `arn:aws:iam::AccountID:role/ZeppelinRole` and add the following permissions to the auto\-created role by console\.

   `"kinesis:GetShardIterator",`

   `"kinesis:GetRecords",`

   `"kinesis:ListShards"`

1. Create a file called `create.json` with the following contents\. Replace the placeholder values with your information\.

   ```
   {
       "ApplicationName": "MyNotebook",
       "RuntimeEnvironment": "ZEPPELIN-FLINK-2_0",
       "ApplicationMode": "INTERACTIVE",
       "ServiceExecutionRole": "arn:aws:iam::AccountID:role/ZeppelinRole",
       "ApplicationConfiguration": {
           "ApplicationSnapshotConfiguration": {
               "SnapshotsEnabled": false
           },
           "ZeppelinApplicationConfiguration": {
               "CatalogConfiguration": {
                   "GlueDataCatalogConfiguration": {
                       "DatabaseARN": "arn:aws:glue:us-east-1:AccountID:database/default"
                   }
               }
           }
       }
   }
   ```

1. Run the following command to create your application:

   ```
   aws kinesisanalyticsv2 create-application --cli-input-json file://create.json 
   ```

1. When the command completes, you see output that shows the details for your new Studio notebook\. The following is an example of the output\.

   ```
   {
       "ApplicationDetail": {
           "ApplicationARN": "arn:aws:kinesisanalytics:us-east-1:012345678901:application/MyNotebook",
           "ApplicationName": "MyNotebook",
           "RuntimeEnvironment": "ZEPPELIN-FLINK-2_0",
           "ApplicationMode": "INTERACTIVE",
           "ServiceExecutionRole": "arn:aws:iam::012345678901:role/ZeppelinRole",
   ...
   ```

1. Run the following command to start your application\. Replace the sample value with your account ID\.

   ```
   aws kinesisanalyticsv2 start-application --application-arn arn:aws:kinesisanalytics:us-east-1:012345678901:application/MyNotebook\
   ```

## Send data to your Kinesis data stream<a name="example-notebook-streams-send"></a>

To send test data to your Kinesis data stream, do the following:

1. Open the [ Kinesis Data Generator](https://awslabs.github.io/amazon-kinesis-data-generator/web/help.html)\. 

1. Choose **Create a Cognito User with CloudFormation**\.

1. The AWS CloudFormation console opens with the Kinesis Data Generator template\. Choose **Next**\.

1. In the **Specify stack details** page, enter a username and password for your Cognito user\. Choose **Next**\.

1. In the **Configure stack options** page, choose **Next**\.

1. In the **Review Kinesis\-Data\-Generator\-Cognito\-User** page, choose the **I acknowledge that AWS CloudFormation might create IAM resources\.** checkbox\. Choose **Create Stack**\.

1. Wait for the AWS CloudFormation stack to finish being created\. After the stack is complete, open the **Kinesis\-Data\-Generator\-Cognito\-User** stack in the AWS CloudFormation console, and choose the **Outputs** tab\. Open the URL listed for the **KinesisDataGeneratorUrl** output value\.

1. In the **Amazon Kinesis Data Generator** page, log in with the credentials you created in step 4\.

1. On the next page, provide the following values:    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/kinesisanalytics/latest/java/example-notebook-streams.html)

   For **Record Template**, paste the following code:

   ```
   {
       "ticker": "{{random.arrayElement(
           ["AMZN","MSFT","GOOG"]
       )}}",
       "price": {{random.number(
           {
               "min":10,
               "max":150
           }
       )}}
   }
   ```

1. Choose **Send data**\.

1. The generator will send data to your Kinesis data stream\. 

   Leave the generator running while you complete the next section\.

## Test your Studio notebook<a name="example-notebook-streams-test"></a>

In this section, you use your Studio notebook to query data from your Kinesis data stream\.

1. Open the Kinesis Data Analytics console at [ https://console\.aws\.amazon\.com/kinesisanalytics/home?region=us\-east\-1\#/applications/dashboard](https://console.aws.amazon.com/kinesisanalytics/home?region=us-east-1#/applications/dashboard)\.

1. On the **Kinesis Data Analytics applications** page, choose the **Studio notebook** tab\. Choose **MyNotebook**\.

1. In the **MyNotebook** page, choose **Open in Apache Zeppelin**\.

   The Apache Zeppelin interface opens in a new tab\.

1. In the **Welcome to Zeppelin\!** page, choose **Zeppelin Note**\.

1. In the **Zeppelin Note** page, enter the following query into a new note:

   ```
   %flink.ssql(type=update)
   select * from stock
   ```

   Choose the run icon\.

   After a short time, the note displays data from the Kinesis data stream\.

To open the Apache Flink Dashboard for your application to view operational aspects, choose **FLINK JOB**\. For more information about the Flink Dashboard, see [Apache Flink Dashboard](https://docs.aws.amazon.com/kinesisanalytics/latest/java/how-dashboard.html) in the [Kinesis Data Analytics Developer Guide](https://docs.aws.amazon.com/)\.

For more examples of Flink Streaming SQL queries, see [Queries](https://nightlies.apache.org/flink/flink-docs-release-1.15/dev/table/sql/queries.html) in the [Apache Flink documentation](https://nightlies.apache.org/flink/flink-docs-release-1.15/)\.