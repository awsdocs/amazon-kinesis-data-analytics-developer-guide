# Application Scaling in Kinesis Data Analytics for Apache Flink<a name="how-scaling"></a>

You can configure the parallel execution of tasks and the allocation of resources for Amazon Kinesis Data Analytics for Apache Flink to implement scaling\. For information about how Apache Flink schedules parallel instances of tasks, see [Parallel Execution](https://nightlies.apache.org/flink/flink-docs-release-1.15/dev/parallel.html) in the [Apache Flink documentation](https://nightlies.apache.org/flink/flink-docs-release-1.15/)\.

**Topics**
+ [Configuring Application Parallelism and ParallelismPerKPU](#how-parallelism)
+ [Allocating Kinesis Processing Units](#how-scaling-kpus)
+ [Updating Your Application's Parallelism](#how-scaling-howto)
+ [Automatic Scaling](#how-scaling-auto)

## Configuring Application Parallelism and ParallelismPerKPU<a name="how-parallelism"></a>

You configure the parallel execution for your Kinesis Data Analytics application tasks \(such as reading from a source or executing an operator\) using the following [https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_ParallelismConfiguration.html](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_ParallelismConfiguration.html) properties: 
+ `Parallelism` — Use this property to set the default Apache Flink application parallelism\. All operators, sources, and sinks execute with this parallelism unless they are overridden in the application code\. The default is `1`, and the default maximum is `256`\.
+ `ParallelismPerKPU` — Use this property to set the number of parallel tasks that can be scheduled per Kinesis Processing Unit \(KPU\) of your application\. The default is `1`, and the maximum is `8`\. For applications that have blocking operations \(for example, I/O\), a higher value of `ParallelismPerKPU` leads to full utilization of KPU resources\.

**Note**  
The limit for `Parallelism` is equal to `ParallelismPerKPU` times the limit for KPUs \(which has a default of 32\)\. The KPUs limit can be increased by requesting a limit increase\. For instructions on how to request a limit increase, see "To request a limit increase" in [Service Quotas](https://docs.aws.amazon.com/general/latest/gr/aws_service_limits.html)\.

For information about setting task parallelism for a specific operator, see [ Setting the Parallelism: Operator](https://nightlies.apache.org/flink/flink-docs-release-1.15/dev/parallel.html#operator-level) in the [Apache Flink documentation](https://nightlies.apache.org/flink/flink-docs-release-1.15/)\.

## Allocating Kinesis Processing Units<a name="how-scaling-kpus"></a>

Kinesis Data Analytics provisions capacity as KPUs\. A single KPU provides you with 1 vCPU and 4 GB of memory\. For every KPU allocated, 50 GB of running application storage is also provided\. 

Kinesis Data Analytics calculates the KPUs that are needed to run your application using the `Parallelism` and `ParallelismPerKPU` properties, as follows:

```
Allocated KPUs for the application = Parallelism/ParallelismPerKPU
```

Kinesis Data Analytics quickly gives your applications resources in response to spikes in throughput or processing activity\. It removes resources from your application gradually after the activity spike has passed\. To disable the automatic allocation of resources, set the `AutoScalingEnabled` value to `false`, as described later in [Updating Your Application's Parallelism](#how-scaling-howto)\. 

The default limit for KPUs for your application is 32\. For instructions on how to request an increase to this limit, see "To request a limit increase" in [Service Quotas](https://docs.aws.amazon.com/general/latest/gr/aws_service_limits.html)\.

## Updating Your Application's Parallelism<a name="how-scaling-howto"></a>

This section contains sample requests for API actions that set an application's parallelism\. For more examples and instructions for how to use request blocks with API actions, see [Kinesis Data Analytics API Example Code](api-examples.md)\.

The following example request for the [https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_CreateApplication.html](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_CreateApplication.html) action sets parallelism when you are creating an application:

```
{
   "ApplicationName": "string",
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
      "FlinkApplicationConfiguration": { 
         "ParallelismConfiguration": { 
            "AutoScalingEnabled": "true",
            "ConfigurationType": "CUSTOM",
            "Parallelism": 4,
            "ParallelismPerKPU": 4
         }
      }
   }
}
```

The following example request for the [https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_UpdateApplication.html](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_UpdateApplication.html) action sets parallelism for an existing application:

```
{
   "ApplicationName": "MyApplication",
   "CurrentApplicationVersionId": 4,
   "ApplicationConfigurationUpdate": { 
      "FlinkApplicationConfigurationUpdate": { 
         "ParallelismConfigurationUpdate": { 
            "AutoScalingEnabledUpdate": "true",
            "ConfigurationTypeUpdate": "CUSTOM",
            "ParallelismPerKPUUpdate": 4,
            "ParallelismUpdate": 4
         }
      }
   }
}
```

The following example request for the [https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_UpdateApplication.html](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_UpdateApplication.html) action disables parallelism for an existing application:

```
{
   "ApplicationName": "MyApplication",
   "CurrentApplicationVersionId": 4,
   "ApplicationConfigurationUpdate": { 
      "FlinkApplicationConfigurationUpdate": { 
         "ParallelismConfigurationUpdate": { 
            "AutoScalingEnabledUpdate": "false"
         }
      }
   }
}
```

## Automatic Scaling<a name="how-scaling-auto"></a>

Kinesis Data Analytics elastically scales your application’s parallelism to accommodate the data throughput of your source and your operator complexity for most scenarios\. Kinesis Data Analytics monitors the resource \(CPU\) usage of your application, and elastically scales your application's parallelism up or down accordingly:
+ Your application scales up \(increases parallelism\) when your CPU usage remains at 75 percent or above for 15 minutes\.
+ Your application scales down \(decreases parallelism\) when your CPU usage remains below 10 percent for six hours\.

Kinesis Data Analytics will not reduce your application's `CurrentParallelism` value to less than your application's `Parallelism` setting\.

When the Kinesis Data Analytics service is scaling your application, it will be in the `AUTOSCALING` status\. You can check your current application status using the [ DescribeApplication](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_DescribeApplication.html) or [ ListApplications](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_ListApplications.html) actions\. While the service is scaling your application, the only valid API action you can use is [ StopApplication](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_ListApplications.html) with the `Force` parameter set to `true`\.

You can use the `AutoScalingEnabled` property \(part of [https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_FlinkApplicationConfiguration.html](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_FlinkApplicationConfiguration.html) \) to enable or disable auto scaling behavior\. Your AWS account is charged for KPUs that Kinesis Data Analytics provisions which is a function of your application's `parallelism` and `parallelismPerKPU` settings\. An activity spike increases your Kinesis Data Analytics costs\.

For information about pricing, see [Amazon Kinesis Data Analytics pricing](https://aws.amazon.com/kinesis/data-analytics/pricing/)\. 

Note the following about application scaling:
+ Automatic scaling is enabled by default\.
+ Scaling doesn't apply to Studio notebooks\. However, if you deploy a Studio notebook as an application with durable state, then scaling will apply to the deployed application\.
+ Your application has a default limit of 32 KPUs\. For more information, see [Quota](limits.md)\.
+ When autoscaling updates application parallelism, the application experiences downtime\. To avoid this downtime, do the following:
  + Disable automatic scaling
  + Configure your application's `parallelism` and `parallelismPerKPU` with the [UpdateApplication](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_UpdateApplication.html) action\. For more information about setting your application's parallelism settings, see [Updating Your Application's Parallelism](#how-scaling-howto) following\.
  + Periodically monitor your application's resource usage to verify that your application has the correct parallelism settings for its workload\. For information about monitoring allocation resource usage, see [Viewing Kinesis Data Analytics Metrics and Dimensions](metrics-dimensions.md)\.

### maxParallelism considerations<a name="how-scaling-auto-max-parallelism"></a>
+ Autoscale logic will prevent scaling a Flink job to a parallelism that will cause interference with the job and operator `maxParallelism`\. For example, if a simple job with only a source and a sink where the source has `maxParallelism` 16 and the `sink` has 8, we will not autoscale the job to above 8\.
+ If `maxParallelism` is not set for a job, Flink will default to 128\. Therefore, if you think that a job will need to run at a higher parallelism than 128, you will have to set that number for your application\.
+ If you expect to see your job autoscale but are not seeing it, ensure your `maxParallelism` values allow for it\.

For additional information, see [Enhanced monitoring and automatic scaling for Apache Flink](https://aws.amazon.com/blogs/big-data/enhanced-monitoring-and-automatic-scaling-for-apache-flink/)

For an example, see [ kda\-flink\-app\-autoscaling](https://github.com/aws-samples/kda-flink-app-autoscaling)\.