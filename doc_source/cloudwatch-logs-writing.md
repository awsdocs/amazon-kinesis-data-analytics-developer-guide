# Writing Custom Messages to CloudWatch Logs<a name="cloudwatch-logs-writing"></a>

You can write custom messages to your Kinesis Data Analytics application's CloudWatch log\. You do this by using the Apache [https://logging.apache.org/log4j/](https://logging.apache.org/log4j/) library or the [https://www.slf4j.org/](https://www.slf4j.org/) library\.

**Topics**
+ [Write to CloudWatch Logs Using Log4J](#cloudwatch-logs-writing-log4j)
+ [Write to CloudWatch Logs Using SLF4J](#cloudwatch-logs-writing-slf4j)

## Write to CloudWatch Logs Using Log4J<a name="cloudwatch-logs-writing-log4j"></a>

1. Add the following dependencies to your application's `pom.xml` file:

   ```
   <dependency>
       <groupId>org.apache.logging.log4j</groupId>
       <artifactId>log4j-api</artifactId>
       <version>2.6.1</version>
   </dependency>
   <dependency>
       <groupId>org.apache.logging.log4j</groupId>
       <artifactId>log4j-core</artifactId>
       <version>2.6.1</version>
   </dependency>
   ```

1. Include the object from the library:

   ```
   import org.apache.logging.log4j.Logger;
   ```

1. Instantiate the `Logger` object, passing in your application class:

   ```
   private static final Logger log = Logger.getLogger(YourApplicationClass.class);
   ```

1. Write to the log using `log.info`\. A large number of messages are written to the application log\. To make your custom messages easier to filter, use the `INFO` application log level\.

   ```
   log.info("This message will be written to the application's CloudWatch log");
   ```

The application writes a record to the log with a message similar to the following:

```
{
  "locationInformation": "com.amazonaws.services.kinesisanalytics.StreamingJob.main(StreamingJob.java:95)", 
  "logger": "com.amazonaws.services.kinesisanalytics.StreamingJob", 
  "message": "This message will be written to the application's CloudWatch log", 
  "threadName": "Flink-DispatcherRestEndpoint-thread-2", 
  "applicationARN": "arn:aws:kinesisanalytics:us-east-1:123456789012:application/test", 
  "applicationVersionId": "1", "messageSchemaVersion": "1", 
  "messageType": "INFO" 
}
```

## Write to CloudWatch Logs Using SLF4J<a name="cloudwatch-logs-writing-slf4j"></a>

1. Add the following dependency to your application's `pom.xml` file:

   ```
   <dependency>
       <groupId>org.slf4j</groupId>
       <artifactId>slf4j-log4j12</artifactId>
       <version>1.7.7</version>
       <scope>runtime</scope>
   </dependency>
   ```

1. Include the objects from the library:

   ```
   import org.slf4j.Logger;
   import org.slf4j.LoggerFactory;
   ```

1. Instantiate the `Logger` object, passing in your application class:

   ```
   private static final Logger log = LoggerFactory.getLogger(YourApplicationClass.class);
   ```

1. Write to the log using `log.info`\. A large number of messages are written to the application log\. To make your custom messages easier to filter, use the `INFO` application log level\.

   ```
   log.info("This message will be written to the application's CloudWatch log");
   ```

The application writes a record to the log with a message similar to the following:

```
{
  "locationInformation": "com.amazonaws.services.kinesisanalytics.StreamingJob.main(StreamingJob.java:95)", 
  "logger": "com.amazonaws.services.kinesisanalytics.StreamingJob", 
  "message": "This message will be written to the application's CloudWatch log", 
  "threadName": "Flink-DispatcherRestEndpoint-thread-2", 
  "applicationARN": "arn:aws:kinesisanalytics:us-east-1:123456789012:application/test", 
  "applicationVersionId": "1", "messageSchemaVersion": "1", 
  "messageType": "INFO" 
}
```