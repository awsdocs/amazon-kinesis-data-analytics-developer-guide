# User\-defined functions<a name="how-zeppelin-udf"></a>

User\-defined functions \(UDFs\) are extension points that allow you to call frequently\-used logic or custom logic that can't be expressed otherwise in queries\. You can use Python or a JVM language like Java or Scala to implement your UDFs in paragraphs inside your Studio notebook\. You can also add to your Studio notebook external JAR files that contain UDFs implemented in a JVM language\. 

To use the console to add UDF JAR files to your Studio notebook, follow these steps:

1. Upload your UDF JAR file to Amazon S3\.

1. In the AWS Management Console, choose the **Custom create** option for creating your Studio notebook\.

1. Follow the Studio notebook creation workflow until you get to the **Configurations** step\.

1. In the **User\-defined functions** section, choose **Add user\-defined function**\.

1. Specify the Amazon S3 location of the JAR file or the ZIP file that has the implementation of your UDF\.

1. Choose **Save changes**\.

To add a UDF JAR when you create a new Studio notebook using the [CreateApplication](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_CreateApplication.html) API, specify the JAR location in the `CustomArtifactConfiguration` data type\. To add a UDF JAR to an existing Studio notebook, invoke the [UpdateApplication](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_UpdateApplication.html) API operation and specify the JAR location in the `CustomArtifactsConfigurationUpdate` data type\. Alternatively, you can use the AWS Management Console to add UDF JAR files to you Studio notebook\.