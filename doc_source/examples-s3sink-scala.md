# Example: Send Streaming Data to Amazon S3 in Scala<a name="examples-s3sink-scala"></a>

**Note**  
Starting from version 1\.15 Flink is Scala free\. Applications can now use the Java API from any Scala version\. Flink still uses Scala in a few key components internally but doesn't expose Scala into the user code classloader\. Because of that, users need to add Scala dependencies into their jar\-archives\.  
For more information about Scala changes in Flink 1\.15, see [ Scala Free in One Fifteen](https://flink.apache.org/2022/02/22/scala-free.html)\.

In this exercise, you will create a simple streaming application which uses Scala 3\.2\.0 and Flink's Java DataStream API\. The application reads data from Kinesis stream, aggregates it using sliding windows and writes results to S3\.

**Note**  
To set up required prerequisites for this exercise, first complete the [Getting Started \(Scala\)](https://docs.aws.amazon.com/kinesisanalytics/latest/java/examples-gs-scala.html) exercise\. You only need to create an additional folder **data/** in the Amazon S3 bucket *ka\-app\-code\-<username>*\. 

**Topics**
+ [Download and Examine the Application Code](#examples-s3sink-scala-download)
+ [Compile and upload the application code](#examples-s3sink-scala-upload)
+ [Create and run the Application \(console\)](#scala-7)
+ [Create and run the application \(CLI\)](#examples-s3sink-scala-create-run-cli)
+ [Update the application code](#examples-s3sink-scala-update-app-code)
+ [Clean Up AWS Resources](#examples-s3sink-scala-cleanup)

## Download and Examine the Application Code<a name="examples-s3sink-scala-download"></a>

The Python application code for this example is available from GitHub\. To download the application code, do the following:

1. Install the Git client if you haven't already\. For more information, see [Installing Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)\. 

1. Clone the remote repository with the following command:

   ```
   git clone https://github.com/aws-samples/amazon-kinesis-data-analytics-java-examples
   ```

1. Navigate to the `amazon-kinesis-data-analytics-java-examples/scala/S3Sink` directory\.

Note the following about the application code:
+ A `build.sbt` file contains information about the application's configuration and dependencies, including the Kinesis Data Analytics libraries\.
+ The `BasicStreamingJob.scala` file contains the main method that defines the application's functionality\.
+ The application uses a Kinesis source to read from the source stream\. The following snippet creates the Kinesis source:

  ```
  private def createSource: FlinkKinesisConsumer[String] = {
    val applicationProperties = KinesisAnalyticsRuntime.getApplicationProperties
    val inputProperties = applicationProperties.get("ConsumerConfigProperties")
  
    new FlinkKinesisConsumer[String](inputProperties.getProperty(streamNameKey, defaultInputStreamName),
      new SimpleStringSchema, inputProperties)
  }
  ```

  The application also uses a StreamingFileSink to write to an Amazon S3 bucket:`

  ```
  def createSink: StreamingFileSink[String] = {
    val applicationProperties = KinesisAnalyticsRuntime.getApplicationProperties
    val s3SinkPath = applicationProperties.get("ProducerConfigProperties").getProperty("s3.sink.path")
  
    StreamingFileSink
      .forRowFormat(new Path(s3SinkPath), new SimpleStringEncoder[String]("UTF-8"))
      .build()
  }
  ```
+ The application creates source and sink connectors to access external resources using a StreamExecutionEnvironment object\.
+ The application creates source and sink connectors using dynamic application properties\. Runtime application's properties are read to configure the connectors\. For more information about runtime properties, see [Runtime Properties](https://docs.aws.amazon.com/kinesisanalytics/latest/java/how-properties.html)\.

## Compile and upload the application code<a name="examples-s3sink-scala-upload"></a>

In this section, you compile and upload your application code to an Amazon S3 bucket\.

**Compile the Application Code**

Use the [SBT](https://www.scala-sbt.org/) build tool to build the Scala code for the application\. To install SBT, see [Install sbt with cs setup](https://www.scala-sbt.org/download.html)\. You also need to install the Java Development Kit \(JDK\)\. See [Prerequisites for Completing the Exercises](https://docs.aws.amazon.com/kinesisanalytics/latest/java/getting-started.html#setting-up-prerequisites)\.

1. To use your application code, you compile and package it into a JAR file\. You can compile and package your code with SBT:

   ```
   sbt assembly
   ```

1. If the application compiles successfully, the following file is created:

   ```
   target/scala-3.2.0/s3-sink-scala-1.0.jar
   ```

**Upload the Apache Flink Streaming Scala Code**

In this section, you create an Amazon S3 bucket and upload your application code\.

1. Open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. Choose **Create bucket**

1. Enter `ka-app-code-<username>` in the **Bucket name** field\. Add a suffix to the bucket name, such as your user name, to make it globally unique\. Choose **Next**\.

1. In **Configure options**, keep the settings as they are, and choose **Next**\.

1. In **Set permissions**, keep the settings as they are, and choose **Next**\.

1. Choose **Create bucket**\.

1. Choose the `ka-app-code-<username>` bucket, and then choose **Upload**\.

1. In the **Select files** step, choose **Add files**\. Navigate to the `s3-sink-scala-1.0.jar` file that you created in the previous step\. 

1. You don't need to change any of the settings for the object, so choose **Upload**\.

Your application code is now stored in an Amazon S3 bucket where your application can access it\.

## Create and run the Application \(console\)<a name="scala-7"></a>

Follow these steps to create, configure, update, and run the application using the console\.

### Create the Application<a name="scala-7-console-create"></a>

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

### Configure the Application<a name="scala-7-console-configure"></a>

Use the following procedure to configure the application\.

**To configure the application**

1. On the **MyApplication** page, choose **Configure**\.

1. On the **Configure application** page, provide the **Code location**:
   + For **Amazon S3 bucket**, enter **ka\-app\-code\-*<username>***\.
   + For **Path to Amazon S3 object**, enter **s3\-sink\-scala\-1\.0\.jar**\.

1. Under **Access to application resources**, for **Access permissions**, choose **Create / update IAM role `kinesis-analytics-MyApplication-us-west-2`**\.

1. Under **Properties**, choose **Add group**\. 

1. Enter the following:    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/kinesisanalytics/latest/java/examples-s3sink-scala.html)

   Choose **Save**\.

1. Under **Properties**, choose **Add group**\. 

1. Enter the following:    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/kinesisanalytics/latest/java/examples-s3sink-scala.html)

1. Under **Monitoring**, ensure that the **Monitoring metrics level** is set to **Application**\.

1. For **CloudWatch logging**, choose the **Enable** check box\.

1. Choose **Update**\.

**Note**  
When you choose to enable Amazon CloudWatch logging, Kinesis Data Analytics creates a log group and log stream for you\. The names of these resources are as follows:   
Log group: `/aws/kinesis-analytics/MyApplication`
Log stream: `kinesis-analytics-log-stream`

### Edit the IAM Policy<a name="scala-7-console-iam"></a>

Edit the IAM policy to add permissions to access the Amazon S3 bucket\.

**To edit the IAM policy to add S3 bucket permissions**

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
           },
           {
               "Sid": "ReadInputStream",
               "Effect": "Allow",
               "Action": "kinesis:*",
               "Resource": "arn:aws:kinesis:us-west-2:012345678901:stream/ExampleInputStream"
           }
       ]
   }
   ```

### Run the Application<a name="scala-7-console-run"></a>

The Flink job graph can be viewed by running the application, opening the Apache Flink dashboard, and choosing the desired Flink job\.

### Stop the Application<a name="scala-7-console-stop"></a>

To stop the application, on the **MyApplication** page, choose **Stop**\. Confirm the action\.

## Create and run the application \(CLI\)<a name="examples-s3sink-scala-create-run-cli"></a>

In this section, you use the AWS Command Line Interface to create and run the Kinesis Data Analytics application\. Use the *kinesisanalyticsv2* AWS CLI command to create and interact with Kinesis Data Analytics applications\.

### Create a permissions policy<a name="examples-s3sink-scala-permissions"></a>

**Note**  
You must create a permissions policy and role for your application\. If you do not create these IAM resources, your application cannot access its data and log streams\. 

First, you create a permissions policy with two statements: one that grants permissions for the read action on the source stream, and another that grants permissions for write actions on the sink stream\. You then attach the policy to an IAM role \(which you create in the next section\)\. Thus, when Kinesis Data Analytics assumes the role, the service has the necessary permissions to read from the source stream and write to the sink stream\.

Use the following code to create the `KAReadSourceStreamWriteSinkStream` permissions policy\. Replace **username** with the user name that you used to create the Amazon S3 bucket to store the application code\. Replace the account ID in the Amazon Resource Names \(ARNs\) **\(012345678901\)** with your account ID\.

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
                "arn:aws:s3:::ka-app-code-username/getting-started-scala-1.0.jar"
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

For step\-by\-step instructions to create a permissions policy, see [Tutorial: Create and Attach Your First Customer Managed Policy](https://docs.aws.amazon.com/IAM/latest/UserGuide/tutorial_managed-policies.html#part-two-create-policy) in the *IAM User Guide*\.

### Create an IAM role<a name="examples-s3sink-scala-iam-policy"></a>

In this section, you create an IAM role that the Kinesis Data Analytics application can assume to read a source stream and write to the sink stream\.

Kinesis Data Analytics cannot access your stream without permissions\. You grant these permissions via an IAM role\. Each IAM role has two policies attached\. The trust policy grants Kinesis Data Analytics permission to assume the role, and the permissions policy determines what Kinesis Data Analytics can do after assuming the role\.

You attach the permissions policy that you created in the preceding section to this role\. 

**To create an IAM role**

1. Open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. In the navigation pane, choose **Roles** and then **Create Role**\.

1. Under **Select type of trusted identity**, choose **AWS Service**

1. Under **Choose the service that will use this role**, choose **Kinesis**\.

1. Under **Select your use case**, choose **Kinesis Analytics**\.

1. Choose **Next: Permissions**\.

1. On the **Attach permissions policies** page, choose **Next: Review**\. You attach permissions policies after you create the role\.

1. On the **Create role** page, enter **KA\-stream\-rw\-role** for the **Role name**\. Choose **Create role**\. 

    Now you have created a new IAM role called `KA-stream-rw-role`\. Next, you update the trust and permissions policies for the role

1. Attach the permissions policy to the role\.
**Note**  
For this exercise, Kinesis Data Analytics assumes this role for both reading data from a Kinesis data stream \(source\) and writing output to another Kinesis data stream\. So you attach the policy that you created in the previous step, [Create a Permissions Policy](https://docs.aws.amazon.com/kinesisanalytics/latest/java/get-started-exercise.html#get-started-exercise-7-cli-policy)\.

   1. On the **Summary** page, choose the **Permissions** tab\.

   1. Choose **Attach Policies**\.

   1. In the search box, enter **KAReadSourceStreamWriteSinkStream** \(the policy that you created in the previous section\)\. 

   1. Choose the `KAReadSourceStreamWriteSinkStream` policy, and choose **Attach policy**\.

You now have created the service execution role that your application uses to access resources\. Make a note of the ARN of the new role\.

For step\-by\-step instructions for creating a role, see [Creating an IAM Role \(Console\)](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_create_for-user.html#roles-creatingrole-user-console) in the *IAM User Guide*\.

### Create the application<a name="examples-s3sink-scala-create-application-cli"></a>

Save the following JSON code to a file named `create_request.json`\. Replace the sample role ARN with the ARN for the role that you created previously\. Replace the bucket ARN suffix \(username\) with the suffix that you chose in the previous section\. Replace the sample account ID \(012345678901\) in the service execution role with your account ID\.

```
{
    "ApplicationName": "s3_sink",
    "ApplicationDescription": "Scala tumbling window application",
    "RuntimeEnvironment": "FLINK-1_15",
    "ServiceExecutionRole": "arn:aws:iam::012345678901:role/KA-stream-rw-role",
    "ApplicationConfiguration": {
        "ApplicationCodeConfiguration": {
            "CodeContent": {
                "S3ContentLocation": {
                    "BucketARN": "arn:aws:s3:::ka-app-code-username",
                    "FileKey": "s3-sink-scala-1.0.jar"
                }
            },
            "CodeContentType": "ZIPFILE"
        },
        "EnvironmentProperties":  { 
         "PropertyGroups": [ 
            { 
               "PropertyGroupId": "ConsumerConfigProperties",
               "PropertyMap" : {
                    "aws.region" : "us-west-2",
                    "stream.name" : "ExampleInputStream",
                    "flink.stream.initpos" : "LATEST"
               }
            },
            { 
               "PropertyGroupId": "ProducerConfigProperties",
               "PropertyMap" : {
                    "s3.sink.path" : "s3a://ka-app-code-<username>/data"
               }
            }
         ]
      }
    },
    "CloudWatchLoggingOptions": [ 
      { 
         "LogStreamARN": "arn:aws:logs:us-west-2:012345678901:log-group:MyApplication:log-stream:kinesis-analytics-log-stream"
      }
   ]
}
```

Execute the [CreateApplication](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_CreateApplication.html) with the following request to create the application:

```
aws kinesisanalyticsv2 create-application --cli-input-json file://create_request.json
```

The application is now created\. You start the application in the next step\.

### Start the Application<a name="examples-s3sink-scala-start"></a>

In this section, you use the [StartApplication](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_StartApplication.html) action to start the application\.

**To start the application**

1. Save the following JSON code to a file named `start_request.json`\.

   ```
   {{
       "ApplicationName": "s3_sink",
       "RunConfiguration": {
           "ApplicationRestoreConfiguration": { 
            "ApplicationRestoreType": "RESTORE_FROM_LATEST_SNAPSHOT"
            }
       }
   }
   ```

1. Execute the `StartApplication` action with the preceding request to start the application:

   ```
   aws kinesisanalyticsv2 start-application --cli-input-json file://start_request.json
   ```

The application is now running\. You can check the Kinesis Data Analytics metrics on the Amazon CloudWatch console to verify that the application is working\.

### Stop the Application<a name="examples-s3sink-scala-stop"></a>

In this section, you use the [StopApplication](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_StopApplication.html) action to stop the application\.

**To stop the application**

1. Save the following JSON code to a file named `stop_request.json`\.

   ```
   {
      "ApplicationName": "s3_sink"
   }
   ```

1. Execute the `StopApplication` action with the preceding request to stop the application:

   ```
   aws kinesisanalyticsv2 stop-application --cli-input-json file://stop_request.json
   ```

The application is now stopped\.

### Add a CloudWatch Logging Option<a name="examples-s3sink-scala-cw-option"></a>

You can use the AWS CLI to add an Amazon CloudWatch log stream to your application\. For information about using CloudWatch Logs with your application, see [Setting Up Application Logging](https://docs.aws.amazon.com/kinesisanalytics/latest/java/cloudwatch-logs.html)\.

### Update Environment Properties<a name="examples-s3sink-scala-update-environment-properties"></a>

In this section, you use the [UpdateApplication](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_UpdateApplication.html) action to change the environment properties for the application without recompiling the application code\. In this example, you change the Region of the source and destination streams\.

**To update environment properties for the application**

1. Save the following JSON code to a file named `update_properties_request.json`\.

   ```
   {"ApplicationName": "s3_sink",
      "CurrentApplicationVersionId": 1,
      "ApplicationConfigurationUpdate": { 
         "EnvironmentPropertyUpdates": { 
            "PropertyGroups": [ 
               { 
                  "PropertyGroupId": "ConsumerConfigProperties",
                  "PropertyMap" : {
                       "aws.region" : "us-west-2",
                       "stream.name" : "ExampleInputStream",
                       "flink.stream.initpos" : "LATEST"
                  }
               },
               { 
                  "PropertyGroupId": "ProducerConfigProperties",
                  "PropertyMap" : {
                       "s3.sink.path" : "s3a://ka-app-code-<username>/data"
                  }
               }
            ]
         }
      }
   }
   ```

1. Execute the `UpdateApplication` action with the preceding request to update environment properties:

   ```
   aws kinesisanalyticsv2 update-application --cli-input-json file://update_properties_request.json
   ```

## Update the application code<a name="examples-s3sink-scala-update-app-code"></a>

When you need to update your application code with a new version of your code package, you use the [UpdateApplication](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_UpdateApplication.html) CLI action\.

**Note**  
To load a new version of the application code with the same file name, you must specify the new object version\. For more information about using Amazon S3 object versions, see [Enabling or Disabling Versioning](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/enable-versioning.html)\.

To use the AWS CLI, delete your previous code package from your Amazon S3 bucket, upload the new version, and call `UpdateApplication`, specifying the same Amazon S3 bucket and object name, and the new object version\. The application will restart with the new code package\.

The following sample request for the `UpdateApplication` action reloads the application code and restarts the application\. Update the `CurrentApplicationVersionId` to the current application version\. You can check the current application version using the `ListApplications` or `DescribeApplication` actions\. Update the bucket name suffix \(<username>\) with the suffix that you chose in the [Create Dependent Resources](examples-gs-scala.md#examples-gs-scala-resources) section\.

```
{
    "ApplicationName": "s3_sink",
    "CurrentApplicationVersionId": 1,
    "ApplicationConfigurationUpdate": {
        "ApplicationCodeConfigurationUpdate": {
            "CodeContentUpdate": {
                "S3ContentLocationUpdate": {
                    "BucketARNUpdate": "arn:aws:s3:::ka-app-code-username",
                    "FileKeyUpdate": "s3-sink-scala-1.0.jar",
                    "ObjectVersionUpdate": "SAMPLEUehYngP87ex1nzYIGYgfhypvDU"
                }
            }
        }
    }
}
```

## Clean Up AWS Resources<a name="examples-s3sink-scala-cleanup"></a>

This section includes procedures for cleaning up AWS resources created in the Tumbling Window tutorial\.

**Topics**
+ [Delete Your Kinesis Data Analytics Application](#examples-s3sink-scala-cleanup-app)
+ [Delete Your Kinesis Data Streams](#examples-s3sink-scala-cleanup-stream)
+ [Delete Your Amazon S3 Object and Bucket](#examples-s3sink-scala-cleanup-s3)
+ [Delete Your IAM Resources](#examples-s3sink-scala-cleanup-iam)
+ [Delete Your CloudWatch Resources](#examples-s3sink-scala-cleanup-cw)

### Delete Your Kinesis Data Analytics Application<a name="examples-s3sink-scala-cleanup-app"></a>

1. Open the Kinesis Data Analytics console at [ https://console\.aws\.amazon\.com/kinesisanalytics](https://console.aws.amazon.com/kinesisanalytics)\.

1. in the Kinesis Data Analytics panel, choose **MyApplication**\.

1. In the application's page, choose **Delete** and then confirm the deletion\.

### Delete Your Kinesis Data Streams<a name="examples-s3sink-scala-cleanup-stream"></a>

1. Open the Kinesis console at [https://console\.aws\.amazon\.com/kinesis](https://console.aws.amazon.com/kinesis)\.

1. In the Kinesis Data Streams panel, choose **ExampleInputStream**\.

1. In the **ExampleInputStream** page, choose **Delete Kinesis Stream** and then confirm the deletion\.

1. In the **Kinesis streams** page, choose the **ExampleOutputStream**, choose **Actions**, choose **Delete**, and then confirm the deletion\.

### Delete Your Amazon S3 Object and Bucket<a name="examples-s3sink-scala-cleanup-s3"></a>

1. Open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. Choose the **ka\-app\-code\-*<username>* bucket\.**

1. Choose **Delete** and then enter the bucket name to confirm deletion\.

### Delete Your IAM Resources<a name="examples-s3sink-scala-cleanup-iam"></a>

1. Open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. In the navigation bar, choose **Policies**\.

1. In the filter control, enter **kinesis**\.

1. Choose the **kinesis\-analytics\-service\-MyApplication\-*<your\-region>*** policy\.

1. Choose **Policy Actions** and then choose **Delete**\.

1. In the navigation bar, choose **Roles**\.

1. Choose the **kinesis\-analytics\-MyApplication\-*<your\-region>*** role\.

1. Choose **Delete role** and then confirm the deletion\.

### Delete Your CloudWatch Resources<a name="examples-s3sink-scala-cleanup-cw"></a>

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. In the navigation bar, choose **Logs**\.

1. Choose the **/aws/kinesis\-analytics/MyApplication** log group\.

1. Choose **Delete Log Group** and then confirm the deletion\.