# Step 3: Create and Run a Kinesis Data Analytics for Apache Flink Application<a name="get-started-exercise"></a>

In this exercise, you create a Kinesis Data Analytics application with data streams as a source and a sink\.

**Topics**
+ [Create Two Amazon Kinesis Data Streams](#get-started-exercise-1)
+ [Write Sample Records to the Input Stream](#get-started-exercise-2)
+ [Download and Examine the Apache Flink Streaming Java Code](#get-started-exercise-5)
+ [Compile the Application Code](#get-started-exercise-5.5)
+ [Upload the Apache Flink Streaming Java Code](#get-started-exercise-6)
+ [Create and Run the Kinesis Data Analytics Application](#get-started-exercise-7)
+ [Next Step](#get-started-exercise-next-step-4)

## Create Two Amazon Kinesis Data Streams<a name="get-started-exercise-1"></a>

Before you create a Kinesis Data Analytics application for this exercise, create two Kinesis data streams \(`ExampleInputStream` and `ExampleOutputStream`\)\. Your application uses these streams for the application source and destination streams\.

You can create these streams using either the Amazon Kinesis console or the following AWS CLI command\. For console instructions, see [Creating and Updating Data Streams](https://docs.aws.amazon.com/kinesis/latest/dev/amazon-kinesis-streams.html) in the *Amazon Kinesis Data Streams Developer Guide*\. 

**To create the data streams \(AWS CLI\)**

1. To create the first stream \(`ExampleInputStream`\), use the following Amazon Kinesis `create-stream` AWS CLI command\.

   ```
   $ aws kinesis create-stream \
   --stream-name ExampleInputStream \
   --shard-count 1 \
   --region us-west-2 \
   --profile adminuser
   ```

1. To create the second stream that the application uses to write output, run the same command, changing the stream name to `ExampleOutputStream`\.

   ```
   $ aws kinesis create-stream \
   --stream-name ExampleOutputStream \
   --shard-count 1 \
   --region us-west-2 \
   --profile adminuser
   ```

## Write Sample Records to the Input Stream<a name="get-started-exercise-2"></a>

In this section, you use a Python script to write sample records to the stream for the application to process\.

**Note**  
This section requires the [AWS SDK for Python \(Boto\)](https://aws.amazon.com/developers/getting-started/python/)\.

1. Create a file named `stock.py` with the following contents:

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
   $ python stock.py
   ```

## Download and Examine the Apache Flink Streaming Java Code<a name="get-started-exercise-5"></a>

The Java application code for this example is available from GitHub\. To download the application code, do the following:

1. Clone the remote repository using the following command:

   ```
   git clone https://github.com/aws-samples/amazon-kinesis-data-analytics-java-examples
   ```

1. Navigate to the `amazon-kinesis-data-analytics-java-examples/GettingStarted` directory\.

Note the following about the application code:
+ A [Project Object Model \(pom\.xml\)](https://maven.apache.org/guides/introduction/introduction-to-the-pom.html) file contains information about the application's configuration and dependencies, including the Kinesis Data Analytics libraries\.
+ The `BasicStreamingJob.java` file contains the `main` method that defines the application's functionality\.
+ The application uses a Kinesis source to read from the source stream\. The following snippet creates the Kinesis source:

  ```
  return env.addSource(new FlinkKinesisConsumer<>(inputStreamName,
                  new SimpleStringSchema(), inputProperties));
  ```
+ Your application creates source and sink connectors to access external resources using a `StreamExecutionEnvironment` object\. 
+ The application creates source and sink connectors using static properties\. To use dynamic application properties, use the `createSourceFromApplicationProperties` and `createSinkFromApplicationProperties` methods to create the connectors\. These methods read the application's properties to configure the connectors\.

  For more information about runtime properties, see [Runtime Properties](how-properties.md)\.

## Compile the Application Code<a name="get-started-exercise-5.5"></a>

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

## Upload the Apache Flink Streaming Java Code<a name="get-started-exercise-6"></a>

In this section, you create an Amazon Simple Storage Service \(Amazon S3\) bucket and upload your application code\.

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

## Create and Run the Kinesis Data Analytics Application<a name="get-started-exercise-7"></a>

You can create and run a Kinesis Data Analytics application using either the console or the AWS CLI\.

**Note**  
When you create the application using the console, your AWS Identity and Access Management \(IAM\) and Amazon CloudWatch Logs resources are created for you\. When you create the application using the AWS CLI, you create these resources separately\.

**Topics**
+ [Create and Run the Application \(Console\)](#get-started-exercise-7-console)
+ [Create and Run the Application \(AWS CLI\)](#get-started-exercise-7-cli)

### Create and Run the Application \(Console\)<a name="get-started-exercise-7-console"></a>

Follow these steps to create, configure, update, and run the application using the console\.

#### Create the Application<a name="get-started-exercise-7-console-create"></a>

1. Open the Kinesis Data Analytics console at [ https://console\.aws\.amazon\.com/kinesisanalytics](https://console.aws.amazon.com/kinesisanalytics)\.

1. On the Kinesis Data Analytics dashboard, choose **Create analytics application**\.

1. On the **Kinesis Analytics \- Create application** page, provide the application details as follows:
   + For **Application name**, enter **MyApplication**\.
   + For **Description**, enter **My java test app**\.
   + For **Runtime**, choose **Apache Flink**\.
   + Leave the version pulldown as **Apache Flink version 1\.15\.2 \(Recommended version\)**\.

1. For **Access permissions**, choose **Create / update IAM role `kinesis-analytics-MyApplication-us-west-2`**\.

1. Choose **Create application**\.

**Note**  
When you create a Kinesis Data Analytics application using the console, you have the option of having an IAM role and policy created for your application\. Your application uses this role and policy to access its dependent resources\. These IAM resources are named using your application name and Region as follows:  
Policy: `kinesis-analytics-service-MyApplication-us-west-2`
Role: `kinesis-analytics-MyApplication-us-west-2`

#### Edit the IAM Policy<a name="get-started-exercise-7-console-iam"></a>

Edit the IAM policy to add permissions to access the Kinesis data streams\.

1. Open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. Choose **Policies**\. Choose the **`kinesis-analytics-service-MyApplication-us-west-2`** policy that the console created for you in the previous section\. 

1. On the **Summary** page, choose **Edit policy**\. Choose the **JSON** tab\.

1. Add the highlighted section of the following policy example to the policy\. Replace the sample account IDs \(*012345678901*\) with your account ID\.

   ```
   {
       "Version": "2012-10-17",
       "Statement": [
           {
               "Sid": "ReadCode",
               "Effect": "Allow",
               "Action": [
                   "s3:GetObject",
                   "s3:GetObjectVersion"
               ],
               "Resource": [
                   "arn:aws:s3:::ka-app-code-username/aws-kinesis-analytics-java-apps-1.0.jar"
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
           },
           {
               "Sid": "ReadInputStream",
               "Effect": "Allow",
               "Action": "kinesis:*",
               "Resource": "arn:aws:kinesis:us-west-2:012345678901:stream/ExampleInputStream"
           },
           {
               "Sid": "WriteOutputStream",
               "Effect": "Allow",
               "Action": "kinesis:*",
               "Resource": "arn:aws:kinesis:us-west-2:012345678901:stream/ExampleOutputStream"
           }
       ]
   }
   ```

#### Configure the Application<a name="get-started-exercise-7-console-configure"></a>

1. On the **MyApplication** page, choose **Configure**\.

1. On the **Configure application** page, provide the **Code location**:
   + For **Amazon S3 bucket**, enter **ka\-app\-code\-*<username>***\.
   + For **Path to Amazon S3 object**, enter **aws\-kinesis\-analytics\-java\-apps\-1\.0\.jar**\.

1. Under **Access to application resources**, for **Access permissions**, choose **Create / update IAM role `kinesis-analytics-MyApplication-us-west-2`**\.

1. Under **Properties**, for **Group ID**, enter **ProducerConfigProperties**\.

1. Enter the following application properties and values:    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/kinesisanalytics/latest/java/get-started-exercise.html)

1. Under **Monitoring**, ensure that the **Monitoring metrics level** is set to **Application**\.

1. For **CloudWatch logging**, select the **Enable** check box\.

1. Choose **Update**\.

**Note**  
When you choose to enable Amazon CloudWatch logging, Kinesis Data Analytics creates a log group and log stream for you\. The names of these resources are as follows:   
Log group: `/aws/kinesis-analytics/MyApplication`
Log stream: `kinesis-analytics-log-stream`

#### Run the Application<a name="get-started-exercise-7-console-run"></a>

The Flink job graph can be viewed by running the application, opening the Apache Flink dashboard, and choosing the desired Flink job\.

#### Stop the Application<a name="get-started-exercise-7-console-stop"></a>

On the **MyApplication** page, choose **Stop**\. Confirm the action\.

#### Update the Application<a name="get-started-exercise-7-console-update"></a>

Using the console, you can update application settings such as application properties, monitoring settings, and the location or file name of the application JAR\. You can also reload the application JAR from the Amazon S3 bucket if you need to update the application code\.

On the **MyApplication** page, choose **Configure**\. Update the application settings and choose **Update**\.

### Create and Run the Application \(AWS CLI\)<a name="get-started-exercise-7-cli"></a>

In this section, you use the AWS CLI to create and run the Kinesis Data Analytics application\. Kinesis Data Analytics for Apache Flink uses the `kinesisanalyticsv2` AWS CLI command to create and interact with Kinesis Data Analytics applications\.

#### Create a Permissions Policy<a name="get-started-exercise-7-cli-policy"></a>

**Note**  
You must create a permissions policy and role for your application\. If you do not create these IAM resources, your application cannot access its data and log streams\.

First, you create a permissions policy with two statements: one that grants permissions for the `read` action on the source stream, and another that grants permissions for `write` actions on the sink stream\. You then attach the policy to an IAM role \(which you create in the next section\)\. Thus, when Kinesis Data Analytics assumes the role, the service has the necessary permissions to read from the source stream and write to the sink stream\.

Use the following code to create the `KAReadSourceStreamWriteSinkStream` permissions policy\. Replace `username` with the user name that you used to create the Amazon S3 bucket to store the application code\. Replace the account ID in the Amazon Resource Names \(ARNs\) \(`012345678901`\) with your account ID\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "S3",
            "Effect": "Allow",
            "Action": [
                "s3:GetObject",
                "s3:GetObjectVersion"
            ],
            "Resource": ["arn:aws:s3:::ka-app-code-username",
                "arn:aws:s3:::ka-app-code-username/*"
            ]
        },
        {
            "Sid": "ReadInputStream",
            "Effect": "Allow",
            "Action": "kinesis:*",
            "Resource": "arn:aws:kinesis:us-west-2:012345678901:stream/ExampleInputStream"
        },
        {
            "Sid": "WriteOutputStream",
            "Effect": "Allow",
            "Action": "kinesis:*",
            "Resource": "arn:aws:kinesis:us-west-2:012345678901:stream/ExampleOutputStream"
        }
    ]
}
```

For step\-by\-step instructions to create a permissions policy, see [Tutorial: Create and Attach Your First Customer Managed Policy](https://docs.aws.amazon.com/IAM/latest/UserGuide/tutorial_managed-policies.html#part-two-create-policy) in the *IAM User Guide*\.

**Note**  
To access other Amazon services, you can use the AWS SDK for Java\. Kinesis Data Analytics automatically sets the credentials required by the SDK to those of the service execution IAM role that is associated with your application\. No additional steps are needed\.

#### Create an IAM Role<a name="get-started-exercise-7-cli-role"></a>

In this section, you create an IAM role that the Kinesis Data Analytics application can assume to read a source stream and write to the sink stream\.

Kinesis Data Analytics cannot access your stream without permissions\. You grant these permissions via an IAM role\. Each IAM role has two policies attached\. The trust policy grants Kinesis Data Analytics permission to assume the role, and the permissions policy determines what Kinesis Data Analytics can do after assuming the role\.

You attach the permissions policy that you created in the preceding section to this role\.

**To create an IAM role**

1. Open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. In the navigation pane, choose **Roles**, **Create Role**\.

1. Under **Select type of trusted identity**, choose **AWS Service**\. Under **Choose the service that will use this role**, choose **Kinesis**\. Under **Select your use case**, choose **Kinesis Analytics**\.

   Choose **Next: Permissions**\.

1. On the **Attach permissions policies** page, choose **Next: Review**\. You attach permissions policies after you create the role\.

1. On the **Create role** page, enter **KA\-stream\-rw\-role** for the **Role name**\. Choose **Create role**\.

   Now you have created a new IAM role called `KA-stream-rw-role`\. Next, you update the trust and permissions policies for the role\.

1. Attach the permissions policy to the role\.
**Note**  
For this exercise, Kinesis Data Analytics assumes this role for both reading data from a Kinesis data stream \(source\) and writing output to another Kinesis data stream\. So you attach the policy that you created in the previous step, [Create a Permissions Policy](#get-started-exercise-7-cli-policy)\.

   1. On the **Summary** page, choose the **Permissions** tab\.

   1. Choose **Attach Policies**\.

   1. In the search box, enter **KAReadSourceStreamWriteSinkStream** \(the policy that you created in the previous section\)\.

   1. Choose the **KAReadSourceStreamWriteSinkStream** policy, and choose **Attach policy**\.

You now have created the service execution role that your application uses to access resources\. Make a note of the ARN of the new role\.

For step\-by\-step instructions for creating a role, see [Creating an IAM Role \(Console\)](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_create_for-user.html#roles-creatingrole-user-console) in the *IAM User Guide*\.

#### Create the Kinesis Data Analytics Application<a name="get-started-exercise-7-cli-create"></a>

1. Save the following JSON code to a file named `create_request.json`\. Replace the sample role ARN with the ARN for the role that you created previously\. Replace the bucket ARN suffix \(`username`\) with the suffix that you chose in the previous section\. Replace the sample account ID \(`012345678901`\) in the service execution role with your account ID\.

   ```
   {
       "ApplicationName": "test",
       "ApplicationDescription": "my java test app",
       "RuntimeEnvironment": "FLINK-1_15",
       "ServiceExecutionRole": "arn:aws:iam::012345678901:role/KA-stream-rw-role",
       "ApplicationConfiguration": {
           "ApplicationCodeConfiguration": {
               "CodeContent": {
                   "S3ContentLocation": {
                       "BucketARN": "arn:aws:s3:::ka-app-code-username",
                       "FileKey": "aws-kinesis-analytics-java-apps-1.0.jar"
                   }
               },
               "CodeContentType": "ZIPFILE"
           },
           "EnvironmentProperties":  { 
            "PropertyGroups": [ 
               { 
                  "PropertyGroupId": "ProducerConfigProperties",
                  "PropertyMap" : {
                       "flink.stream.initpos" : "LATEST",
                       "aws.region" : "us-west-2",
                       "AggregationEnabled" : "false"
                  }
               },
               { 
                  "PropertyGroupId": "ConsumerConfigProperties",
                  "PropertyMap" : {
                       "aws.region" : "us-west-2"
                  }
               }
            ]
         }
       }
   }
   ```

1. Execute the [https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_CreateApplication.html](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_CreateApplication.html) action with the preceding request to create the application: 

   ```
   aws kinesisanalyticsv2 create-application --cli-input-json file://create_request.json
   ```

The application is now created\. You start the application in the next step\.

#### Start the Application<a name="get-started-exercise-7-cli-start"></a>

In this section, you use the [https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_StartApplication.html](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_StartApplication.html) action to start the application\.

**To start the application**

1. Save the following JSON code to a file named `start_request.json`\.

   ```
   {
       "ApplicationName": "test",
       "RunConfiguration": {
           "ApplicationRestoreConfiguration": { 
            "ApplicationRestoreType": "RESTORE_FROM_LATEST_SNAPSHOT"
            }
       }
   }
   ```

1. Execute the [https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_StartApplication.html](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_StartApplication.html) action with the preceding request to start the application:

   ```
   aws kinesisanalyticsv2 start-application --cli-input-json file://start_request.json
   ```

The application is now running\. You can check the Kinesis Data Analytics metrics on the Amazon CloudWatch console to verify that the application is working\.

#### Stop the Application<a name="get-started-exercise-7-cli-stop"></a>

In this section, you use the [https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_StopApplication.html](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_StopApplication.html) action to stop the application\.

**To stop the application**

1. Save the following JSON code to a file named `stop_request.json`\.

   ```
   {
      "ApplicationName": "test"
   }
   ```

1. Execute the [https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_StopApplication.html](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_StopApplication.html) action with the following request to stop the application:

   ```
   aws kinesisanalyticsv2 stop-application --cli-input-json file://stop_request.json
   ```

The application is now stopped\.

#### Add a CloudWatch Logging Option<a name="get-started-exercise-7-cli-cw"></a>

You can use the AWS CLI to add an Amazon CloudWatch log stream to your application\. For information about using CloudWatch Logs with your application, see [Setting Up Application Logging](cloudwatch-logs.md)\.

#### Update Environment Properties<a name="get-started-exercise-7-cli-update-env"></a>

In this section, you use the [https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_UpdateApplication.html](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_UpdateApplication.html) action to change the environment properties for the application without recompiling the application code\. In this example, you change the Region of the source and destination streams\.

**To update environment properties for the application**

1. Save the following JSON code to a file named `update_properties_request.json`\.

   ```
   {"ApplicationName": "test",
      "CurrentApplicationVersionId": 1,
      "ApplicationConfigurationUpdate": { 
         "EnvironmentPropertyUpdates": { 
            "PropertyGroups": [ 
               { 
                  "PropertyGroupId": "ProducerConfigProperties",
                  "PropertyMap" : {
                       "flink.stream.initpos" : "LATEST",
                       "aws.region" : "us-west-2",
                       "AggregationEnabled" : "false"
                  }
               },
               { 
                  "PropertyGroupId": "ConsumerConfigProperties",
                  "PropertyMap" : {
                       "aws.region" : "us-west-2"
                  }
               }
            ]
         }
      }
   }
   ```

1. Execute the [https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_UpdateApplication.html](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_UpdateApplication.html) action with the preceding request to update environment properties:

   ```
   aws kinesisanalyticsv2 update-application --cli-input-json file://update_properties_request.json
   ```

#### Update the Application Code<a name="get-started-exercise-7-cli-update-code"></a>

When you need to update your application code with a new version of your code package, you use the [https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_UpdateApplication.html](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_UpdateApplication.html) AWS CLI action\.

**Note**  
To load a new version of the application code with the same file name, you must specify the new object version\. For more information about using Amazon S3 object versions, see [Enabling or Disabling Versioning](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/enable-versioning.html)\.

To use the AWS CLI, delete your previous code package from your Amazon S3 bucket, upload the new version, and call `UpdateApplication`, specifying the same Amazon S3 bucket and object name, and the new object version\. The application will restart with the new code package\.

The following sample request for the `UpdateApplication` action reloads the application code and restarts the application\. Update the `CurrentApplicationVersionId` to the current application version\. You can check the current application version using the `ListApplications` or `DescribeApplication` actions\. Update the bucket name suffix \(*<username>*\) with the suffix that you chose in the [Create Two Amazon Kinesis Data Streams](#get-started-exercise-1) section\.

```
{
    "ApplicationName": "test",
    "CurrentApplicationVersionId": 1,
    "ApplicationConfigurationUpdate": {
        "ApplicationCodeConfigurationUpdate": {
            "CodeContentUpdate": {
                "S3ContentLocationUpdate": {
                    "BucketARNUpdate": "arn:aws:s3:::ka-app-code-username",
                    "FileKeyUpdate": "aws-kinesis-analytics-java-apps-1.0.jar",
                    "ObjectVersionUpdate": "SAMPLEUehYngP87ex1nzYIGYgfhypvDU"
                }
            }
        }
    }
}
```

## Next Step<a name="get-started-exercise-next-step-4"></a>

[Step 4: Clean Up AWS Resources](getting-started-cleanup.md)