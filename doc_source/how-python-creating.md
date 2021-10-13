# Creating Your Python Kinesis Data Analytics Application<a name="how-python-creating"></a>

## Specifying your Code Files<a name="how-python-creating-code"></a>

Once you have created your application's code package, you upload it to an Amazon S3 bucket\. You then create your application using either the console or the [CreateApplication](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_CreateApplication.html) action\.

When you create your application using the [CreateApplication](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_CreateApplication.html) action, you specify the code files and archives in your zip file using a special application property group called `kinesis.analytics.flink.run.options`\. You can define the following types files:
+ **python**: A text file containing a Python main method\.
+ **jarfile**: A Java JAR file containing Java user\-defined functions\.
+ **pyFiles**: A Python resource file containing resources to be used by the application\.
+ **pyArchives**: A zip file containing resource files for the application\.

For more information about Apache Flink Python code file types, see [ Command Line Usage](https://ci.apache.org/projects/flink/flink-docs-release-1.11/ops/cli.html#usage) in the [ Apache Flink Documentation](https://ci.apache.org/projects/flink/flink-docs-release-1.11/)\.

**Note**  
Kinesis Data Analytics does not support the `pyModule`, `pyExecutable`, or `pyRequirements` file types\. All of the code, requirements, and dependencies must be in your zip file\. You can't specify dependencies to be installed using pip\. 

The following example json snippet demonstrates how to specify file locations within your application's zip file:

```
"ApplicationConfiguration": {
    "EnvironmentProperties": {
      "PropertyGroups": [
        {
          "PropertyGroupId": "kinesis.analytics.flink.run.options",
          "PropertyMap": {
            "python": "MyApplication/main.py",
            "jarfile": "MyApplication/lib/myJarFile.jar",
            "pyFiles": "MyApplication/lib/myDependentFile.py",
            "pyArchives": "MyApplication/lib/myArchive.zip"
          }
        },
```