# User\-defined functions<a name="how-zeppelin-udf"></a>

User\-defined functions \(UDFs\) are extension points that allow you to call frequently\-used logic or custom logic that can't be expressed otherwise in queries\. You can use Python or a JVM language like Java or Scala to implement your UDFs in paragraphs inside your Studio notebook\. You can also add to your Studio notebook external JAR files that contain UDFs implemented in a JVM language\. 

When implementing JARs that register abstract classes that subclass `UserDefinedFunction` \(or your own abstract classes\), use provided scope in Apache Maven, `compileOnly` dependency declarations in Gradle, provided scope in SBT, or an equivalent directive in your UDF project build configuration\. This allows the UDF source code to compile against the Flink APIs, but the Flink API classes are not themselves included in the build artifacts\. Refer to this [pom](https://github.com/aws-samples/kinesis-udfs-textanalytics/blob/ec27108faa48f1a4c5d173ed3a2ef4565b58b5b5/kinesis-udfs-textanalytics-linear/pom.xml#L47) from the UDF jar example which adheres to such prerequisite on a Maven project\. 

**Note**  
For an example setup, see [Translate, redact and analyze streaming data using SQL functions with Amazon Kinesis Data Analytics, Amazon Translate, and Amazon Comprehend](https://aws.amazon.com/blogs/machine-learning/translate-redact-and-analyze-streaming-data-using-sql-functions-with-amazon-kinesis-data-analytics-amazon-translate-and-amazon-comprehend/) on the *AWS Machine Learning Blog*\.

To use the console to add UDF JAR files to your Studio notebook, follow these steps:

1. Upload your UDF JAR file to Amazon S3\.

1. In the AWS Management Console, choose the **Custom create** option for creating your Studio notebook\.

1. Follow the Studio notebook creation workflow until you get to the **Configurations** step\.

1. In the **User\-defined functions** section, choose **Add user\-defined function**\.

1. Specify the Amazon S3 location of the JAR file or the ZIP file that has the implementation of your UDF\.

1. Choose **Save changes**\.

To add a UDF JAR when you create a new Studio notebook using the [CreateApplication](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_CreateApplication.html) API, specify the JAR location in the `CustomArtifactConfiguration` data type\. To add a UDF JAR to an existing Studio notebook, invoke the [UpdateApplication](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_UpdateApplication.html) API operation and specify the JAR location in the `CustomArtifactsConfigurationUpdate` data type\. Alternatively, you can use the AWS Management Console to add UDF JAR files to you Studio notebook\.

## Considerations with user\-defined functions<a name="how-zeppelin-udf-considerations"></a>
+ Kinesis Data Analytics Studio uses the [Apache Zeppelin terminology](https://zeppelin.apache.org/docs/0.9.0/quickstart/explore_ui.html) wherein a notebook is a Zeppelin instance that can contain multiple notes\. Each note can then contain multiple paragraphs\. With Kinesis Data Analytics Studio the interpreter process is shared across all the notes in the notebook\. So if you perform an explicit function registration using [createTemporarySystemFunction](https://nightlies.apache.org/flink/flink-docs-master/api/java/org/apache/flink/table/api/TableEnvironment.html#createTemporarySystemFunction-java.lang.String-java.lang.Class-) in one note, the same can be referenced as\-is in another note of same notebook\. 

  The *Deploy as application* operation however works on an *individual* note and not all notes in the notebook\. When you perform deploy as application, only active note's contents are used to generate the application\. Any explicit function registration performed in other notebooks are not part of the generated application dependencies\. Additionally, during Deploy as application option an implicit function registration occurs by converting the main class name of JAR to a lowercase string\.

   For example, if `TextAnalyticsUDF` is the main class for UDF JAR, then an implicit registration will result in function name `textanalyticsudf`\. So if an explicit function registration in note 1 of Studio occurs like the following, then all other notes in that notebook \(say note 2\) can refer the function by name `myNewFuncNameForClass` because of the shared interpreter:

  `stenv.createTemporarySystemFunction("myNewFuncNameForClass", new TextAnalyticsUDF())`

   However during deploy as application operation on note 2, this explicit registration *will not be included* in the dependencies and hence the deployed application will not perform as expected\. Because of the implicit registration, by default all references to this function is expected to be with `textanalyticsudf` and not `myNewFuncNameForClass`\.

   If there is a need for custom function name registration then note 2 itself is expected to contain another paragraph to perform another explicit registration as follows: 

  ```
  %flink(parallelism=l)
  import com.amazonaws.kinesis.udf.textanalytics.TextAnalyticsUDF 
  # re-register the JAR for UDF with custom name
  stenv.createTemporarySystemFunction("myNewFuncNameForClass", new TextAnalyticsUDF())
  ```

  ```
  %flink. ssql(type=update, parallelism=1) 
  INSERT INTO
      table2
  SELECT
      myNewFuncNameForClass(column_name)
  FROM
      table1
  ;
  ```
+ If your UDF JAR includes Flink SDKs, then configure your Java project so that the UDF source code can compile against the Flink SDKs, but the Flink SDK classes are not themselves included in the build artifact, for example the JAR\. 

  You can use `provided` scope in Apache Maven, `compileOnly` dependency declarations in Gradle, `provided` scope in SBT, or equivalent directive in their UDF project build configuration\. You can refer to this [pom](https://github.com/aws-samples/kinesis-udfs-textanalytics/blob/ec27108faa48f1a4c5d173ed3a2ef4565b58b5b5/kinesis-udfs-textanalytics-linear/pom.xml#L47) from the UDF jar example, which adheres to such a prerequisite on a maven project\. For a complete step\-by\-step tutorial, see this [AWS Machine Learning Blog](https://aws.amazon.com/blogs/machine-learning/translate-redact-and-analyze-streaming-data-using-sql-functions-with-amazon-kinesis-data-analytics-amazon-translate-and-amazon-comprehend/)\.