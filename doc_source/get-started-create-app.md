# Step 3\.1: Create an Application<a name="get-started-create-app"></a>

In this section, you create an Amazon Kinesis Data Analytics application\. You configure application input in the next step\.

**To create a data analytics application**

1. Sign in to the AWS Management Console and open the Kinesis Data Analytics console at [ https://console\.aws\.amazon\.com/kinesisanalytics](https://console.aws.amazon.com/kinesisanalytics)\.

1. Choose **Create application**\.

1. On the **Create application** page, type an application name, type a description, choose **SQL** for the application's **Runtime** setting, and then choose **Create application**\.  
![\[Screenshot of New application page with application name and description.\]](http://docs.aws.amazon.com/kinesisanalytics/latest/dev/images/gs-v2-10.png)

   Doing this creates a Kinesis data analytics application with a status of READY\. The console shows the application hub where you can configure input and output\.
**Note**  
To create an application, the [CreateApplication](API_CreateApplication.md) operation requires only the application name\. You can add input and output configuration after you create an application in the console\.

   In the next step, you configure input for the application\. In the input configuration, you add a streaming data source to the application and discover a schema for an in\-application input stream by sampling data on the streaming source\.

**Next Step**  
[Step 3\.2: Configure Input](get-started-configure-input.md)