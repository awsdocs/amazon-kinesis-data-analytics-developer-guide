# Connectors and dependencies<a name="how-zeppelin-connectors"></a>

Connectors enable you to read and write data across various technologies\. Kinesis Data Analytics bundles three default connectors with your Studio notebook\. You can also use custom connectors\. For more information about connectors, see [Table & SQL Connectors](https://ci.apache.org/projects/flink/flink-docs-release-1.13/docs/connectors/table/overview/) in the Apache Flink documentation\.

## Default connectors<a name="zeppelin-default-connectors"></a>

If you use the AWS Management Console to create your Studio notebook, Kinesis Data Analytics includes the following custom connectors by default: `flink-sql-connector-flink`, `flink-connector-kafka_2.12` and `aws-msk-iam-auth`\. To create a Studio notebook through the console without these custom connectors, choose the **Create with custom settings** option\. Then, when you get to the **Configurations** page, clear the checkboxes next to the two connectors\.

If you use the [CreateApplication](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_CreateApplication.html) API to create your Studio notebook, the `flink-sql-connector-flink` and `flink-connector-kafka` connectors aren't included by default\. To add them, specify them as a `MavenRefernce` in the `CustomArtifactsConfiguration` data type as shown in the following examples\.

The `aws-msk-iam-auth` connector is the connector to use with Amazon MSK that includes the feature to automatically authenticate with IAM\. 

**Note**  
The connector versions shown in the following example are the only versions that we support\.

```
For the Kinesis connector:

"CustomArtifactsConfiguration": [{
   "ArtifactType": "DEPENDENCY_JAR",            
   "MavenReference": { 
      "GroupId": "org.apache.flink",
      "ArtifactId": "flink-sql-connector-kinesis-2.12",
      "Version": "1.13.2"
   }      
}]

For the Apache MSK connector:

"CustomArtifactsConfiguration": [{
   "ArtifactType": "DEPENDENCY_JAR",            
   "MavenReference": { 
      "GroupId": "software.amazon.msk",
      "ArtifactId": "aws-msk-iam-auth",
      "Version": "1.1.0"
   }      
}]
            
For the Apache Kafka connector:  

"CustomArtifactsConfiguration": [{
   "ArtifactType": "DEPENDENCY_JAR",            
   "MavenReference": { 
      "GroupId": "org.apache.flink",
      "ArtifactId": "flink-connector-kafka_2.12",
      "Version": "1.13.2"
   }      
}]
```

To add these connectors to an existing notebook, use the [UpdateApplication](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_UpdateApplication.html) API operation and specify them as a `MavenReference` in the `CustomArtifactsConfigurationUpdate` data type\.

**Note**  
You can set `failOnError` to true for the `flink-sql-connector-kinesis` connector in the table API\.

## Dependencies and custom connectors<a name="zeppelin-custom-connectors"></a>

To use the AWS Management Console to add a dependency or a custom connector to your Studio notebook, follow these steps:

1. Upload your custom connector's file to Amazon S3\.

1. In the AWS Management Console, choose the **Custom create** option for creating your Studio notebook\.

1. Follow the Studio notebook creation workflow until you get to the **Configurations** step\.

1. In the **Custom connectors** section, choose **Add custom connector**\.

1. Specify the Amazon S3 location of the dependency or the custom connector\.

1. Choose **Save changes**\.

To add a dependency JAR or a custom connector when you create a new Studio notebook using the [CreateApplication](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_CreateApplication.html) API, specify the Amazon S3 location of the dependency JAR or the custom connector in the `CustomArtifactsConfiguration` data type\. To add a dependency or a custom connector to an existing Studio notebook, invoke the [UpdateApplication](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_UpdateApplication.html) API operation and specify the Amazon S3 location of the dependency JAR or the custom connector in the `CustomArtifactsConfigurationUpdate` data type\.

**Note**  
When you include a dependency or a custom connector, you must also include all its transitive dependencies that aren't bundled within it\.