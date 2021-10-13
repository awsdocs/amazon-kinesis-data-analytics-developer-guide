# Identity and Access Management in Amazon Kinesis Data Analytics for Apache Flink<a name="how-iam"></a>



Amazon Kinesis Data Analytics needs permissions to read records from a streaming source that you specify in your application configuration\. Kinesis Data Analytics also needs permissions to write your application output to sinks that you specify in your application configuration\. 

**Note**  
You must create a permissions policy and role for your application\. If you do not create these AWS Identity and Access Management \(IAM\) resources, your application cannot access its data sources, data destinations, and log streams\.

You can grant these permissions by creating an IAM role that Kinesis Data Analytics can assume\. Permissions that you grant to this role determine what Kinesis Data Analytics can do when the service assumes the role\. 



**Note**  
The information in this section is useful if you want to create an IAM role yourself\. When you create an application in the Kinesis Data Analytics console, the console can create an IAM role for you then\. The console uses the following naming convention for IAM roles that it creates\.  

```
kinesis-analytics-ApplicationName
```
After the role is created, you can review the role and attached policies in the AWS Identity and Access Management \(IAM\) console\. 

Each IAM role has two policies attached to it\. In the trust policy, you specify who can assume the role\. In the permissions policy \(there can be one or more\), you specify the permissions that you want to grant to this role\. The following sections describe these policies, which you can use when you create an IAM role\. 

**Topics**
+ [Trust Policy](#iam-role-trust-policy)
+ [Permissions Policy](#iam-role-permissions-policy)

## Trust Policy<a name="iam-role-trust-policy"></a>

To grant Kinesis Data Analytics permissions to assume a role to access a streaming or reference source, you can attach the following trust policy to an IAM role\.

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

## Permissions Policy<a name="iam-role-permissions-policy"></a>

If you are creating an IAM role to allow Kinesis Data Analytics to read from an application's streaming source, you must grant permissions for relevant read actions\. Examples of streaming sources include a Kinesis data stream, an Amazon Kinesis Data Firehose delivery stream, or a reference source in an Amazon Simple Storage Service \(Amazon S3\) bucket\. Depending on your source, you can attach the following permissions policy\.



### Permissions Policy for Reading a Kinesis Data Stream<a name="iam-role-permissions-policy-stream"></a>

In the following example, replace each *user input placeholder* with your own information\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "ReadInputKinesis",
            "Effect": "Allow",
            "Action": [
                "kinesis:DescribeStream",
                "kinesis:GetShardIterator",
                "kinesis:GetRecords",
                "kinesis:ListShards"
            ],
            "Resource": [
                "arn:aws:kinesis:aws-region:aws-account-id:stream/inputStreamName"
            ]
        }
    ]
}
```

### Permissions Policy for Writing to a Kinesis Data Stream<a name="iam-role-permissions-policy-ak-stream"></a>

In the following example, replace each *user input placeholder* with your own information\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "WriteOutputKinesis",
            "Effect": "Allow",
            "Action": [
                "kinesis:DescribeStream",
                "kinesis:PutRecord",
                "kinesis:PutRecords"
            ],
            "Resource": [
                "arn:aws:kinesis:aws-region:aws-account-id:stream/output-stream-name"
            ]
        }
    ]
}
```

### Permissions Policy for Writing to a Kinesis Data Firehose Delivery Stream<a name="iam-role-permissions-policy-af-delivery-stream"></a>

In the following example, replace each *user input placeholder* with your own information\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "WriteOutputFirehose",
            "Effect": "Allow",
            "Action": [
                "firehose:DescribeDeliveryStream",
                "firehose:PutRecord",
                "firehose:PutRecordBatch"
            ],
            "Resource": [
                "arn:aws:firehose:aws-region:aws-account-id:deliverystream/output-firehose-name"
            ]
        }
    ]
}
```

### Permissions Policy for Reading from an Amazon S3 Bucket<a name="iam-role-permissions-policy-reference"></a>



```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "s3:Get*",
        "s3:List*"
      ],
      "Resource": "*"
    }
  ]
}
```

### Permissions Policy for Writing to a CloudWatch Log Stream<a name="iam-role-permissions-policy-reference"></a>

For information about adding permissions to write to a CloudWatch log stream, see [Adding Permissions to Write to the CloudWatch Log Stream](cloudwatch-logs.md#enable_putlogevents)\.