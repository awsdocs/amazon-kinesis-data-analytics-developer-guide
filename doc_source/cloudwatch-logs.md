# Working with Amazon CloudWatch Logs<a name="cloudwatch-logs"></a>

If an Amazon Kinesis Data Analytics application is misconfigured, it can transition to a running state during application start\. Or it can update but not process any data into the in\-application input stream\. By adding a CloudWatch log option to the application, you can monitor for application configuration problems\.

Amazon Kinesis Data Analytics can generate configuration errors under the following conditions:
+ The Kinesis data stream used for input doesn't exist\.
+ The Amazon Kinesis Data Firehose delivery stream used for input doesn't exist\.
+ The Amazon S3 bucket used as a reference data source doesn't exist\.
+ The specified file in the reference data source in the S3 bucket doesn't exist\.
+ The correct resource is not defined in the AWS Identity and Access Management \(IAM\) role that manages related permissions\.
+ The correct permission is not defined in the IAM role that manages related permissions\.
+ Kinesis Data Analytics doesn't have permission to assume the IAM role that manages related permissions\.

For more information about Amazon CloudWatch, see the [http://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/](http://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/)\.

## Adding the PutLogEvents Policy Action<a name="enable_putlogevents"></a>

Amazon Kinesis Data Analytics needs permissions to write misconfiguration errors to CloudWatch\. You can add these permissions to the IAM role that Amazon Kinesis Data Analytics assumes, as described following\. For more information on using an IAM role for Amazon Kinesis Data Analytics, see [Identity and Access Management in Kinesis Data Analytics](iam-role.md)\.

### Trust Policy<a name="enable_putlogevents_trust_policy"></a>

To grant Kinesis Data Analytics permissions to assume an IAM role, you can attach the following trust policy to the role\.

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "Service": "kinesisanalytics.amazonaws.com"
      },
      "Action": "sts:AssumeRole"
    }
  ]
}
```

### Permissions Policy<a name="enable_putlogevents_permissions_policy"></a>

To grant an application permissions to write log events to CloudWatch from a Kinesis Data Analytics resource, you can use the following IAM permissions policy\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "Stmt0123456789000",
            "Effect": "Allow",
            "Action": [
                "logs:PutLogEvents"
            ],
            "Resource": [
                "arn:aws:logs:us-east-1:123456789012:log-group:my-log-group:log-stream:my-log-stream*"
            ]
        }
    ]
}
```

## Adding Configuration Error Monitoring<a name="adding_cloudwatch"></a>

Use the following API actions to add a CloudWatch log option to a new or existing application or change a log option for an existing application\.

**Note**  
You can currently only add a CloudWatch log option to an application by using API actions\. You can't add CloudWatch log options by using the console\.

### Adding a CloudWatch Log Option When Creating an Application<a name="add_cloudwatch_create"></a>

The following code example demonstrates how to use the `CreateApplication` action to add a CloudWatch log option when you create an application\. For more information on `Create_Application`, see [CreateApplication](API_CreateApplication.md)\.

```
{
   "ApplicationCode": "<The SQL code the new application will run on the input stream>",
   "ApplicationDescription": "<A friendly description for the new application>",
   "ApplicationName": "<The name for the new application>",
   "Inputs": [ ... ],
   "Outputs": [ ... ],
   "CloudWatchLoggingOptions": [{
      "LogStreamARN": "<Amazon Resource Name (ARN) of the CloudWatch log stream to add to the new application>",
      "RoleARN": "<ARN of the role to use to access the log>"
	}]
}
```

### Adding a CloudWatch Log Option to an Existing Application<a name="add_to_existing_app"></a>

The following code example demonstrates how to use the `AddApplicationCloudWatchLoggingOption` action to add a CloudWatch log option to an existing application\. For more information about `AddApplicationCloudWatchLoggingOption`, see [AddApplicationCloudWatchLoggingOption](API_AddApplicationCloudWatchLoggingOption.md)\.

```
{
   "ApplicationName": "<Name of the application to add the log option to>",
   "CloudWatchLoggingOption": { 
      "LogStreamARN": "<ARN of the log stream to add to the application>",
      "RoleARN": "<ARN of the role to use to access the log>"
   },
   "CurrentApplicationVersionId": <Version of the application to add the log to>
}
```

### Updating an Existing CloudWatch Log Option<a name="update_existing"></a>

The following code example demonstrates how to use the `UpdateApplication` action to modify an existing CloudWatch log option\. For more information about `UpdateApplication`, see [UpdateApplication](API_UpdateApplication.md)\.

```
{
   "ApplicationName": "<Name of the application to update the log option for>",
   "ApplicationUpdate": { 
      "CloudWatchLoggingOptionUpdates": [ 
         { 
            "CloudWatchLoggingOptionId": "<ID of the logging option to modify>",
            "LogStreamARNUpdate": "<ARN of the new log stream to use>",
            "RoleARNUpdate": "<ARN of the new role to use to access the log stream>"
         }
      ],
   },
   "CurrentApplicationVersionId": <ID of the application version to modify>
}
```

### Deleting a CloudWatch Log Option from an Application<a name="delete-log"></a>

The following code example demonstrates how to use the `DeleteApplicationCloudWatchLoggingOption` action to delete an existing CloudWatch log option\. For more information about `DeleteApplicationCloudWatchLoggingOption`, see [DeleteApplicationCloudWatchLoggingOption](API_DeleteApplicationCloudWatchLoggingOption.md)\.

```
{
   "ApplicationName": "<Name of application to delete log option from>",
   "CloudWatchLoggingOptionId": "<ID of the application log option to delete>",
   "CurrentApplicationVersionId": <Version of the application to delete the log option from>
}
```

## Configuration Errors<a name="cloudwatch_errors"></a>

The following sections contain details about errors that you might see in Amazon CloudWatch Logs from a misconfigured application\.

### Error Message Format<a name="cloudwatch_errors_format"></a>

Error messages generated by application misconfiguration appear in the following format\.

```
{
"applicationARN": "string",
"applicationVersionId": integer,
"messageType": "ERROR",
"message": "string",
"inputId": "string",
"referenceId": "string",
"errorCode": "string"
"messageSchemaVersion": "integer",
}
```

The fields in an error message contain the following information:
+ `applicationARN`: The Amazon Resource Name \(ARN\) of the generating application, for example: `arn:aws:kinesisanalytics:us-east-1:112233445566:application/sampleApp`
+ `applicationVersionId`: The version of the application at the time the error was encountered\. For more information, see [ApplicationDetail](API_ApplicationDetail.md)\.
+ `messageType`: The message type\. Currently, this type can be only `ERROR`\. 
+ `message`: The details of the error, for example:

  ```
  There is a problem related to the configuration of your input. Please check that the resource exists, the role has the correct permissions to access the resource and that Kinesis Analytics can assume the role provided.
  ```
+ `inputId`: The ID associated with the application input\. This value is only present if this input is the cause of the error\. This value is not present if `referenceId` is present\. For more information, see [DescribeApplication](API_DescribeApplication.md)\. 
+ `referenceId`: The ID associated with the application reference data source\. This value is only present if this source is the cause of the error\. This value is not present if `inputId` is present\. For more information, see [DescribeApplication](API_DescribeApplication.md)\. 
+ `errorCode`: The identifier for the error\. This ID is either `InputError` or `ReferenceDataError`\.
+ `messageSchemaVersion`: A value that specifies the current message schema version, currently `1`\. You can check this value to see if the error message schema has been updated\.

### Errors<a name="cloudwatch_errors_list"></a>

The errors that might appear in CloudWatch Logs for Amazon Kinesis Data Analytics include the following\. 

#### Resource Does Not Exist<a name="resource_does_not_exist"></a>

If an ARN is specified for a Kinesis input stream that doesn't exist, but the ARN is syntactically correct, an error like the following is generated\.

```
{
"applicationARN": "arn:aws:kinesisanalytics:us-east-1:112233445566:application/sampleApp",
"applicationVersionId": "5",
 "messageType": "ERROR",
 "message": "There is a problem related to the configuration of your input. Please check that the resource exists, the role has the correct permissions to access the resource and that Kinesis Analytics can assume the role provided.",
 "inputId":"1.1",
 "errorCode": "InputError",
 "messageSchemaVersion": "1"
}
```

If an incorrect Amazon S3 file key is used for reference data, an error like the following is generated\.

```
{
 "applicationARN": "arn:aws:kinesisanalytics:us-east-1:112233445566:application/sampleApp",
 "applicationVersionId": "5",
 "messageType": "ERROR",
 "message": "There is a problem related to the configuration of your reference data. Please check that the bucket and the file exist, the role has the correct permissions to access these resources and that Kinesis Analytics can assume the role provided.",
 "referenceId":"1.1",
 "errorCode": "ReferenceDataError",
 "messageSchemaVersion": "1"
}
```

#### Role Does Not Exist<a name="role_does_not_exist"></a>

If an ARN is specified for an IAM input role that doesn't exist, but the ARN is syntactically correct, an error like the following is generated\.

```
{
 "applicationARN": "arn:aws:kinesisanalytics:us-east-1:112233445566:application/sampleApp",
 "applicationVersionId": "5",
 "messageType": "ERROR",
 "message": "There is a problem related to the configuration of your input. Please check that the resource exists, the role has the correct permissions to access the resource and that Kinesis Analytics can assume the role provided.",
 "inputId":null,
 "errorCode": "InputError",
 "messageSchemaVersion": "1"
}
```

#### Role Does Not Have Permissions to Access the Resource<a name="role_does_not_have_permissions"></a>

If an input role is used that doesn't have permission to access the input resources, such as a Kinesis source stream, an error like the following is generated\.

```
{
 "applicationARN": "arn:aws:kinesisanalytics:us-east-1:112233445566:application/sampleApp",
 "applicationVersionId": "5",
 "messageType": "ERROR",
 "message": "There is a problem related to the configuration of your input. Please check that the resource exists, the role has the correct permissions to access the resource and that Kinesis Analytics can assume the role provided.",
 "inputId":null,
 "errorCode": "InputError",
 "messageSchemaVersion": "1"
}
```