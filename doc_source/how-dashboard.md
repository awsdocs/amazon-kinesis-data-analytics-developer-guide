# Using the Apache Flink Dashboard with Amazon Kinesis Data Analytics<a name="how-dashboard"></a>

You can use your application's Apache Flink Dashboard to monitor your Kinesis Data Analytics application's health\. Your application's dashboard shows the following information:
+ Resources in use, including Task Managers and Task Slots\. 
+ Information about Jobs, including those that are running, completed, canceled, and failed\. 

For information about Apache Flink Task Managers, Task Slots, and Jobs, see [Apache Flink Architecture](https://nightlies.apache.org/flink/flink-docs-release-1.15/concepts/flink-architecture.html#taskmanagers) on the Apache Flink website\. 

Note the following about using the Apache Flink Dashboard with Kinesis Data Analytics applications:
+ The Apache Flink Dashboard for Kinesis Data Analytics applications is read\-only\. You can't make changes to your Kinesis Data Analytics application using the Apache Flink Dashboard\.
+ The Apache Flink Dashboard is not compatible with Microsoft Internet Explorer\.

![\[Screenshot of the Apache Flink Dashboard\]](http://docs.aws.amazon.com/kinesisanalytics/latest/java/images/dashboard.png)

## Accessing Your Application's Apache Flink Dashboard<a name="how-dashboard-accessing"></a>

You can access your application's Apache Flink Dashboard either through the Kinesis Data Analytics console, or by requesting a secure URL endpoint using the CLI\.

### Accessing Your Application's Apache Flink Dashboard Using the Kinesis Data Analytics Console<a name="how-dashboard-accessing-console"></a>

To access your application's Apache Flink Dashboard from the console, choose **Apache Flink Dashboard** on your application's page\.

![\[Diagram showing location of the Apache Flink Dashboard button.\]](http://docs.aws.amazon.com/kinesisanalytics/latest/java/images/dashboard_button.png)

**Note**  
When you open the dashboard from the Kinesis Data Analytics console, the URL that the console generates will be valid for 12 hours\.

### Accessing your application's Apache Flink Dashboard using the Kinesis Data Analytics CLI<a name="how-dashboard-accessing-cli"></a>

You can use the Kinesis Data Analytics CLI to generate a URL to access your application dashboard\. The URL that you generate is valid for a specified amount of time\.

**Note**  
If you don't access the generated URL within three minutes, it will no longer be valid\.

You generate your dashboard URL using the [ CreateApplicationPresignedUrl](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_CreateApplicationPresignedUrl.html) action\. You specify the following parameters for the action: 
+ The application name
+ The time in seconds that the URL will be valid
+ You specify `FLINK_DASHBOARD_URL` as the URL type\.