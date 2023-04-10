# Appendix: Creating custom IAM policies<a name="how-zeppelin-appendix-iam"></a>

You normally use managed IAM policies to allow your application to access dependent resources\. If you need finer control over your application's permissions, you can use a custom IAM policy\. This section contains examples of custom IAM policies\.

**Note**  
In the following policy examples, replace the placeholder text with your application's values\.

**Topics**
+ [AWS Glue](#how-zeppelin-iam-glue)
+ [CloudWatch Logs](#how-zeppelin-iam-cw)
+ [Kinesis streams](#how-zeppelin-iam-streams)
+ [Amazon MSK clusters](#how-zeppelin-iam-msk)

## AWS Glue<a name="how-zeppelin-iam-glue"></a>

The following example policy grants permissions to access a AWS Glue database\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "GlueTable",
            "Effect": "Allow",
            "Action": [            
                "glue:GetConnection",
                "glue:GetTable",
                "glue:GetTables",
                "glue:GetDatabase",
                "glue:CreateTable",
                "glue:UpdateTable"
            ],
            "Resource": [
                "arn:aws:glue:<region>:<accountId>:connection/*",
                "arn:aws:glue:<region>:<accountId>:table/<database-name>/*",
                "arn:aws:glue:<region>:<accountId>:database/<database-name>",
                "arn:aws:glue:<region>:<accountId>:database/hive",
                "arn:aws:glue:<region>:<accountId>:catalog"
            ]
        },
        {
            "Sid": "GlueDatabase",
            "Effect": "Allow",
            "Action": "glue:GetDatabases",
            "Resource": "*"
        }
    ]
}
```

## CloudWatch Logs<a name="how-zeppelin-iam-cw"></a>

The following policy grants permissions to access CloudWatch Logs:

```
{
      "Sid": "ListCloudwatchLogGroups",
      "Effect": "Allow",
      "Action": [
        "logs:DescribeLogGroups"
      ],
      "Resource": [
        "arn:aws:logs:<region>:<accountId>:log-group:*"
      ]
    },
    {
      "Sid": "ListCloudwatchLogStreams",
      "Effect": "Allow",
      "Action": [
        "logs:DescribeLogStreams"
      ],
      "Resource": [
        "<logGroupArn>:log-stream:*"
      ]
    },
    {
      "Sid": "PutCloudwatchLogs",
      "Effect": "Allow",
      "Action": [
        "logs:PutLogEvents"
      ],
      "Resource": [
        "<logStreamArn>"
      ]
    }
```

**Note**  
If you create your application using the console, the console adds the necessary policies to access CloudWatch Logs to your application role\.

## Kinesis streams<a name="how-zeppelin-iam-streams"></a>

Your application can use a Kinesis Stream for a source or a destination\. Your application needs read permissions to read from a source stream, and write permissions to write to a destination stream\.

The following policy grants permissions to read from a Kinesis Stream used as a source:

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "KinesisShardDiscovery",
      "Effect": "Allow",
      "Action": "kinesis:ListShards",
      "Resource": "*"
    },
    {
      "Sid": "KinesisShardConsumption",
      "Effect": "Allow",
      "Action": [
        "kinesis:GetShardIterator",
        "kinesis:GetRecords",
        "kinesis:DescribeStream",
        "kinesis:DescribeStreamSummary",
        "kinesis:RegisterStreamConsumer",
        "kinesis:DeregisterStreamConsumer"
      ],
      "Resource": "arn:aws:kinesis:<region>:<accountId>:stream/<stream-name>"
    },
    {
      "Sid": "KinesisEfoConsumer",
      "Effect": "Allow",
      "Action": [
        "kinesis:DescribeStreamConsumer",
        "kinesis:SubscribeToShard"
      ],
      "Resource": "arn:aws:kinesis:<region>:<account>:stream/<stream-name>/consumer/*"
    }
  ]
}
```

The following policy grants permissions to write to a Kinesis Stream used as a destination:

```
{
    "Version": "2012-10-17",
    "Statement": [
         {
            "Sid": "KinesisStreamSink",
            "Effect": "Allow",
            "Action": [
                "kinesis:PutRecord",
                "kinesis:PutRecords",
                "kinesis:DescribeStreamSummary",
                "kinesis:DescribeStream"
            ],
            "Resource": "arn:aws:kinesis:<region>:<accountId>:stream/<stream-name>"
        }
    ]
}
```

If your application accesses an encypted Kinesis stream, you must grant additional permissions to access the stream and the stream's encryption key\. 

The following policy grants permissions to access an encrypted source stream and the stream's encryption key:

```
{
      "Sid": "ReadEncryptedKinesisStreamSource",
      "Effect": "Allow",
      "Action": [
        "kms:Decrypt"
      ],
      "Resource": [
        "<inputStreamKeyArn>"
      ]
    }
    ,
```

The following policy grants permissions to access an encrypted destination stream and the stream's encryption key:

```
{
      "Sid": "WriteEncryptedKinesisStreamSink",
      "Effect": "Allow",
      "Action": [
        "kms:GenerateDataKey"
      ],
      "Resource": [
        "<outputStreamKeyArn>"
      ]
    }
```

## Amazon MSK clusters<a name="how-zeppelin-iam-msk"></a>

To grant access to an Amazon MSK cluster, you grant access to the cluster's VPC\. For policy examples for accessing an Amazon VPC, see  [ VPC Application Permissions](https://docs.aws.amazon.com/kinesisanalytics/latest/java/vpc-permissions.html)\.