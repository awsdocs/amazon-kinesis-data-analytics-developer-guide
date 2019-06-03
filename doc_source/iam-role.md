# Identity and Access Management in Kinesis Data Analytics<a name="iam-role"></a>

Amazon Kinesis Data Analytics needs permissions to read records from a streaming source that you specify in your application input configuration\. Amazon Kinesis Data Analytics also needs permissions to write your application output to streams that you specify in your application output configuration\. 

You can grant these permissions by creating an IAM role that Amazon Kinesis Data Analytics can assume\. Permissions that you grant to this role determine what Amazon Kinesis Data Analytics can do when the service assumes the role\. 

**Note**  
The information in this section is useful if you want to create an IAM role yourself\. When you create an application in the Amazon Kinesis Data Analytics console, the console can create an IAM role for you at that time\. The console uses the following naming convention for IAM roles that it creates:  

```
kinesis-analytics-ApplicationName
```
After the role is created, you can review the role and attached policies in the IAM console\. 

Each IAM role has two policies attached to it\. In the trust policy, you specify who can assume the role\. In the permissions policy \(there can be one or more\), you specify the permissions that you want to grant to this role\. The following sections describe these policies, which you can use when you create an IAM role\. 

## Trust Policy<a name="iam-role-trust-policy"></a>

To grant Amazon Kinesis Data Analytics permissions to assume a role to access a streaming or reference source, you can attach the following trust policy to an IAM role:

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

If you are creating an IAM role to allow Amazon Kinesis Data Analytics to read from an application's streaming source, you must grant permissions for relevant read actions\. Depending on your source \(for example, an Kinesis stream, a Kinesis Data Firehose delivery stream, or a reference source in an Amazon S3 bucket\), you can attach the following permissions policy\.

### Permissions Policy for Reading an Kinesis Stream<a name="iam-role-permissions-policy-stream"></a>

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

### Permissions Policy for Reading a Kinesis Data Firehose Delivery Stream<a name="iam-role-permissions-policy-delivery-stream"></a>

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "ReadInputFirehose",
            "Effect": "Allow",
            "Action": [
                "firehose:DescribeDeliveryStream",
                "firehose:Get*"
            ],
            "Resource": [
                "arn:aws:firehose:aws-region:aws-account-id:deliverystream/inputFirehoseName"
            ]
        }
    ]
}
```

**Note**  
The `firehose:Get*` permission refers to an internal accessor that Kinesis Data Analytics uses to access the stream\. There is no public accessor for a Kinesis Data Firehose delivery stream\.

If you direct Amazon Kinesis Data Analytics to write output to external destinations in your application output configuration, you need to grant the following permission to the IAM role\. 

### Permissions Policy for Writing to a Kinesis Stream<a name="iam-role-permissions-policy-ak-stream"></a>

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

### Permissions Policy for Writing to a Firehose Delivery Stream<a name="iam-role-permissions-policy-af-delivery-stream"></a>

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

### Permissions Policy for Reading a Reference Data Source from an Amazon S3 Bucket<a name="iam-role-permissions-policy-reference"></a>

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