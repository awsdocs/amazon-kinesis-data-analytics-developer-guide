# Data Protection in Amazon Kinesis Data Analytics for SQL Applications<a name="data-protection"></a>

You can protect your data using tools that are provided by AWS\. Kinesis Data Analytics can work with services that support encrypting data, including Kinesis Data Streams, Kinesis Data Firehose, and Amazon S3\. 

## Data Encryption in Kinesis Data Analytics<a name="data-encryption"></a>

### Encryption at Rest<a name="encryption-at-rest"></a>

Note the following about encrypting data at rest with Kinesis Data Analytics:
+ You can encrypt data on the incoming Kinesis data stream using [StartStreamEncryption](https://docs.aws.amazon.com/kinesis/latest/APIReference/API_StartStreamEncryption.html)\. For more information, see [What Is Server\-Side Encryption for Kinesis Data Streams?](https://docs.aws.amazon.com/streams/latest/dev/what-is-sse.html)\.
+ Output data can be encrypted at rest using Kinesis Data Firehose to store data in an encrypted Amazon S3 bucket\. You can specify the encryption key that your Amazon S3 bucket uses\. For more information, see [Protecting Data Using Server\-Side Encryption with AWS KMS–Managed Keys \(SSE\-KMS\)](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingKMSEncryption.html)\.
+ Your application's code is encrypted at rest\.
+ Your application's reference data is encrypted at rest\.

### Encryption In Transit<a name="encryption-in-transit"></a>

Kinesis Data Analytics encrypts all data in transit\. Encryption in transit is enabled for all Kinesis Data Analytics applications and cannot be disabled\. 

Kinesis Data Analytics encrypts data in transit in the following scenarios:
+ Data in transit from Kinesis Data Streams to Kinesis Data Analytics\.
+ Data in transit between internal components within Kinesis Data Analytics\.
+ Data in transit between Kinesis Data Analytics and Kinesis Data Firehose\.

### Key Management<a name="key-management"></a>

Data encryption in Kinesis Data Analytics uses service\-managed keys\. Customer\-managed keys are not supported\.