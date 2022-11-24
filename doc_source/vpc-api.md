# Kinesis Data Analytics VPC API<a name="vpc-api"></a>

Use the following Kinesis Data Analytics API operations to manage VPCs for your application\. For information on using the Kinesis Data Analytics API, see [Kinesis Data Analytics API Example Code](api-examples.md)\.

## CreateApplication<a name="vpc-api-create"></a>

Use the [CreateApplication](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_CreateApplication.html) action to add a VPC configuration to your application during creation\.

The following example request code for the `CreateApplication` action includes a VPC configuration when the application is created:

```
{
  "ApplicationName":"MyApplication",
  "ApplicationDescription":"My-Application-Description",
  "RuntimeEnvironment":"FLINK-1_15",
  "ServiceExecutionRole":"arn:aws:iam::123456789123:role/myrole",
  "ApplicationConfiguration": {
    "ApplicationCodeConfiguration":{
      "CodeContent":{
        "S3ContentLocation":{
          "BucketARN":"arn:aws:s3:::mybucket",
          "FileKey":"myflink.jar",
          "ObjectVersion":"AbCdEfGhIjKlMnOpQrStUvWxYz12345"
        }
      },
      "CodeContentType":"ZIPFILE"
    },
      "FlinkApplicationConfiguration":{
      "ParallelismConfiguration":{
        "ConfigurationType":"CUSTOM",
        "Parallelism":2,
        "ParallelismPerKPU":1,
        "AutoScalingEnabled":true
      }
    },
  "VpcConfigurations": [ 
         { 
            "SecurityGroupIds": [ "sg-0123456789abcdef0" ],
            "SubnetIds": [ "subnet-0123456789abcdef0" ]
         }
      ]
  }
}
```

## AddApplicationVpcConfiguration<a name="vpc-api-add"></a>

Use the [AddApplicationVpcConfiguration](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_AddApplicationVpcConfiguration.html) action to add a VPC configuration to your application after it has been created\.

The following example request code for the `AddApplicationVpcConfiguration` action adds a VPC configuration to an existing application:

```
{
   "ApplicationName": "MyApplication",
   "CurrentApplicationVersionId": 9,
   "VpcConfiguration": { 
      "SecurityGroupIds": [ "sg-0123456789abcdef0" ],
      "SubnetIds": [ "subnet-0123456789abcdef0" ]
   }
}
```

## DeleteApplicationVpcConfiguration<a name="vpc-api-del"></a>

Use the [DeleteApplicationVpcConfiguration](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_DeleteApplicationVpcConfiguration.html) action to remove a VPC configuration from your application\.

The following example request code for the `AddApplicationVpcConfiguration` action removes an existing VPC configuration from an application:

```
{
   "ApplicationName": "MyApplication",
   "CurrentApplicationVersionId": 9,
   "VpcConfigurationId": "1.1"
}
```

## UpdateApplication<a name="vpc-api-upd"></a>

Use the [UpdateApplication](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_UpdateApplication.html) action to update all of an application's VPC configurations at once\.

The following example request code for the `UpdateApplication` action updates all of the VPC configurations for an application:

```
{
   "ApplicationConfigurationUpdate": { 
      "VpcConfigurationUpdates": [ 
         { 
            "SecurityGroupIdUpdates": [ "sg-0123456789abcdef0" ],
            "SubnetIdUpdates": [ "subnet-0123456789abcdef0" ],
            "VpcConfigurationId": "2.1"
         }
      ]
   },
   "ApplicationName": "MyApplication",
   "CurrentApplicationVersionId": 9
}
```