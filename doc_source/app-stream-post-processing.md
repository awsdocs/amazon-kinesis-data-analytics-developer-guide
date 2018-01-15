# Examples: Post Processing In\-Application Stream<a name="app-stream-post-processing"></a>

In your Amazon Kinesis Data Analytics application, you can create in\-application streams to store intermediate results of analytics\. Post processing refers to persisting the results stored in application streams to external destinations for further analysis\. 

In your application configuration, you can configure output to persist data in your in\-application streams to external destinations, such as a Kinesis data stream or a Kinesis data delivery stream, for further analysis\. 

For example, if application output is persisted to a Kinesis data stream, you can configure AWS Lambda to poll the stream and invoke a Lambda function to process records on the stream\. 


+ [Example: Integrating Amazon Kinesis Data Analytics with AWS Lambda](aws-lambda-integration.md)