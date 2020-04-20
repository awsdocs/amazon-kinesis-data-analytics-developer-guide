# Creating Lambda Functions for Application Destinations<a name="how-it-works-output-lambda-functions"></a>

Your Kinesis Data Analytics application can use AWS Lambda functions as an output\. Kinesis Data Analytics provides templates for creating Lambda functions to use as a destination for your applications\. Use these templates as a starting point for post\-processing output from your application\. 

**Topics**
+ [Creating a Lambda Function Destination in Node\.js](#how-it-works-lambda-dest-nodejs)
+ [Creating a Lambda Function Destination in Python](#how-it-works-lambda-dest-python)
+ [Creating a Lambda Function Destination in Java](#how-it-works-lambda-dest-java)
+ [Creating a Lambda Function Destination in \.NET](#how-it-works-lambda-net)

## Creating a Lambda Function Destination in Node\.js<a name="how-it-works-lambda-dest-nodejs"></a>

The following template for creating a destination Lambda function in Node\.js is available on the console:


| Lambda as Output Blueprint | Language and Version | Description | 
| --- | --- | --- | 
| kinesis\-analytics\-output | Node\.js 6\.10 | Deliver output records from a Kinesis Data Analytics application to a custom destination\. | 

## Creating a Lambda Function Destination in Python<a name="how-it-works-lambda-dest-python"></a>

The following templates for creating a destination Lambda function in Python are available on the console:


| Lambda as Output Blueprint | Language and Version | Description | 
| --- | --- | --- | 
| kinesis\-analytics\-output\-sns | Python 2\.7 | Deliver output records from a Kinesis Data Analytics application to Amazon SNS\. | 
| kinesis\-analytics\-output\-ddb | Python 2\.7 | Deliver output records from a Kinesis Data Analytics application to Amazon DynamoDB\. | 

## Creating a Lambda Function Destination in Java<a name="how-it-works-lambda-dest-java"></a>

To create a destination Lambda function in Java, use the [Java events](https://github.com/aws/aws-lambda-java-libs/tree/master/aws-lambda-java-events/src/main/java/com/amazonaws/services/lambda/runtime/events) classes\.

The following code demonstrates a sample destination Lambda function using Java:

```
public class LambdaFunctionHandler
        implements RequestHandler<KinesisAnalyticsOutputDeliveryEvent, KinesisAnalyticsOutputDeliveryResponse> {

    @Override
    public KinesisAnalyticsOutputDeliveryResponse handleRequest(KinesisAnalyticsOutputDeliveryEvent event,
            Context context) {
        context.getLogger().log("InvocatonId is : " + event.invocationId);
        context.getLogger().log("ApplicationArn is : " + event.applicationArn);

        List<KinesisAnalyticsOutputDeliveryResponse.Record> records = new ArrayList<KinesisAnalyticsOutputDeliveryResponse.Record>();
        KinesisAnalyticsOutputDeliveryResponse response = new KinesisAnalyticsOutputDeliveryResponse(records);

        event.records.stream().forEach(record -> {
            context.getLogger().log("recordId is : " + record.recordId);
            context.getLogger().log("record retryHint is :" + record.lambdaDeliveryRecordMetadata.retryHint);
            // Add logic here to transform and send the record to final destination of your choice.
            response.records.add(new Record(record.recordId, KinesisAnalyticsOutputDeliveryResponse.Result.Ok));
        });
        return response;
    }

}
```

## Creating a Lambda Function Destination in \.NET<a name="how-it-works-lambda-net"></a>

To create a destination Lambda function in \.NET, use the [\.NET events ](https://github.com/aws/aws-lambda-dotnet/tree/master/Libraries/src/Amazon.Lambda.KinesisAnalyticsEvents) classes\.

The following code demonstrates a sample destination Lambda function using C\#:

```
public class Function
    {
        public KinesisAnalyticsOutputDeliveryResponse FunctionHandler(KinesisAnalyticsOutputDeliveryEvent evnt, ILambdaContext context)
        {
            context.Logger.LogLine($"InvocationId: {evnt.InvocationId}");
            context.Logger.LogLine($"ApplicationArn: {evnt.ApplicationArn}");

            var response = new KinesisAnalyticsOutputDeliveryResponse
            {
                Records = new List<KinesisAnalyticsOutputDeliveryResponse.Record>()
            };

            foreach (var record in evnt.Records)
            {
                context.Logger.LogLine($"\tRecordId: {record.RecordId}");
                context.Logger.LogLine($"\tRetryHint: {record.RecordMetadata.RetryHint}");
                context.Logger.LogLine($"\tData: {record.DecodeData()}");

                // Add logic here to send to the record to final destination of your choice.

                var deliveredRecord = new KinesisAnalyticsOutputDeliveryResponse.Record
                {
                    RecordId = record.RecordId,
                    Result = KinesisAnalyticsOutputDeliveryResponse.OK
                };
                response.Records.Add(deliveredRecord);
            }
            return response;
        }
    }
```

For more information about creating Lambda functions for pre\-processing and destinations in \.NET, see [https://github.com/aws/aws-lambda-dotnet/tree/master/Libraries/src/Amazon.Lambda.KinesisAnalyticsEvents](https://github.com/aws/aws-lambda-dotnet/tree/master/Libraries/src/Amazon.Lambda.KinesisAnalyticsEvents)\.