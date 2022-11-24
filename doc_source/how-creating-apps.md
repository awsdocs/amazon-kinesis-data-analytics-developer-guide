# Creating a Kinesis Data Analytics for Apache Flink Application<a name="how-creating-apps"></a>

This topic contains information about creating a Kinesis Data Analytics for Apache Flink application\.

**Topics**
+ [Building your Kinesis Data Analytics Application Code](#how-creating-apps-building)
+ [Creating your Kinesis Data Analytics Application](#how-creating-apps-creating)
+ [Starting your Kinesis Data Analytics Application](#how-creating-apps-starting)
+ [Verifying your Kinesis Data Analytics Application](#how-creating-apps-verifying)
+ [Creating Kinesis Data Analytics applications with Apache Beam](how-creating-apps-beam.md)

## Building your Kinesis Data Analytics Application Code<a name="how-creating-apps-building"></a>

This section describes the components you use to build the application code for your Kinesis Data Analytics application\. 

We recommend that you use the latest supported version of Apache Flink for your application code\. The latest version of Apache Flink that Kinesis Data Analytics supports is **1\.13\.2**\. For information about upgrading Kinesis Data Analytics applications, see [Upgrading Applications](earlier.md#earlier-upgrading)\. 

You build your application code using [Apache Maven](https://maven.apache.org/)\. An Apache Maven project uses a `pom.xml` file to specify the versions of components that it uses\. 

**Note**  
Kinesis Data Analytics supports JAR files up to 512 MB in size\. If you use a JAR file larger than this, your application will fail to start\.

Use the following component versions for Kinesis Data Analytics applications:


****  

| Component | Version | 
| --- | --- | 
| Java | 11 \(recommended\) | 
| Scala | 2\.12 | 
| Kinesis Data Analytics for Flink Runtime \(aws\-kinesisanalytics\-runtime\) | 1\.2\.0 | 
| Kinesis Data Analytics Flink Connectors \(aws\-kinesisanalytics\-flink\) | 2\.0\.0 | 
| AWS Kinesis Connector \(flink\-connector\-kinesis\) | [1\.13\.2](https://search.maven.org/artifact/org.apache.flink/flink-connector-kinesis_2.12/1.13.2/jar) | 
| Apache Beam \(Beam Applications Only\) | 2\.33\.0, with Jackson version 2\.12\.2 | 

For an example of a `pom.xml` file for a Kinesis Data Analytics application that uses Apache Flink version 1\.13\.2, see the [Kinesis Data Analytics Getting Started Application\.](https://github.com/aws-samples/amazon-kinesis-data-analytics-java-examples/blob/master/GettingStarted/pom.xml)

For information about creating a Kinesis Data Analytics application that uses **Apache Beam**, see [Using Apache Beam](how-creating-apps-beam.md)\.

### Specifying your Application's Apache Flink Version<a name="how-creating-apps-building-flink"></a>

When using Kinesis Data Analytics for Flink Runtime version 1\.1\.0 and later, you specify the version of Apache Flink that your application uses when you compile your application\. You provide the version of Apache Flink with the `-Dflink.version` parameter as follows:

```
mvn package -Dflink.version=1.13.2
```

For building applications with older versions of Apache Flink, see [Earlier Versions](earlier.md)\.

## Creating your Kinesis Data Analytics Application<a name="how-creating-apps-creating"></a>

Once you have built your application code, you do the following to create your Kinesis Data Analytics application:
+ **Upload your Application code**: Upload your application code to an Amazon S3 bucket\. You specify the S3 bucket name and object name of your application code when you create your application\. For a tutorial that shows how to upload your application code, see [Upload the Apache Flink Streaming Java Code](get-started-exercise.md#get-started-exercise-6) in the [Getting Started \(DataStream API\)](getting-started.md) tutorial\.
+ **Create your Kinesis Data Analytics application**: Use one of the following methods to create your Kinesis Data Analytics application:
  + **Create your Kinesis Data Analytics application using the AWS console:** You can create and configure your application using the AWS console\. 

    When you create your application using the console, your application's dependent resources \(such as CloudWatch Logs streams, IAM roles, and IAM policies\) are created for you\. 

    When you create your application using the console, you specify what version of Apache Flink your application uses by selecting it from the pull\-down on the **Kinesis Analytics \- Create application** page\. 

    For a tutorial about how to use the console to create an application, see [Create and Run the Application \(Console\)](get-started-exercise.md#get-started-exercise-7-console) in the [Getting Started \(DataStream API\)](getting-started.md) tutorial\.
  + **Create your Kinesis Data Analytics application using the AWS CLI:** You can create and configure your application using the AWS CLI\. 

    When you create your application using the CLI, you must also create your application's dependent resources \(such as CloudWatch Logs streams, IAM roles, and IAM policies\) manually\.

    When you create your application using the CLI, you specify what version of Apache Flink your application uses by using the `RuntimeEnvironment` parameter of the `CreateApplication` action\.

    For a tutorial about how to use the CLI to create an application, see [Create and Run the Application \(AWS CLI\)](get-started-exercise.md#get-started-exercise-7-cli) in the [Getting Started \(DataStream API\)](getting-started.md) tutorial\.
**Note**  
You cannot change the `RuntimeEnvironment` of an existing application\. If you need to change the `RuntimeEnvironment` of an existing application, you must delete the application and create it again\.

## Starting your Kinesis Data Analytics Application<a name="how-creating-apps-starting"></a>

After you have built your application code, uploaded it to S3, and created your Kinesis Data Analytics application, you then start your application\. Starting a Kinesis Data Analytics application typically takes several minutes\.

Use one of the following methods to start your application:
+ **Start your Kinesis Data Analytics application using the AWS console:** You can run your application by choosing **Run** on your application's page in the AWS console\.
+ **Start your Kinesis Data Analytics application using the AWS API:** You can run your application using the [StartApplication](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_StartApplication.html) action\. 

## Verifying your Kinesis Data Analytics Application<a name="how-creating-apps-verifying"></a>

You can verify that your application is working in the following ways:
+ **Using CloudWatch Logs:** You can use CloudWatch Logs and CloudWatch Logs Insights to verify that your application is running properly\. For information about using CloudWatch Logs with your Kinesis Data Analytics application, see [Logging and Monitoring](monitoring-overview.md)\.
+ **Using CloudWatch Metrics:** You can use CloudWatch Metrics to monitor your application's activity, or activity in the resources your application uses for input or output \(such as Kinesis streams, Kinesis Data Firehose delivery streams, or Amazon S3 buckets\.\) For more information about CloudWatch metrics, see [Working with Metrics](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/working_with_metrics.html) in the Amazon CloudWatch User Guide\.
+ **Monitoring Output Locations:** If your application writes output to a location \(such as an Amazon S3 bucket or database\), you can monitor that location for written data\.