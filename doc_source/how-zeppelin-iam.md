# IAM permissions for Studio notebooks<a name="how-zeppelin-iam"></a>

Kinesis Data Analytics creates an IAM role for you when you create a Studio notebook through the AWS Management Console\. It also associates with that role a policy that allows the following access:


****  

| Service | Access  | 
| --- | --- | 
| CloudWatch Logs | List | 
| Amazon EC2 | List | 
| AWS Glue | Read, Write | 
| Kinesis Data Analytics | Read | 
| Kinesis Data Analytics V2 | Read | 
| Amazon S3 | Read, Write | 