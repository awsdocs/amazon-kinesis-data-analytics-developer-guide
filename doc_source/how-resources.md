# Application Resources<a name="how-resources"></a>

This section describes the system resources that your application uses\. Understanding how Kinesis Data Analytics provisions and uses resources will help you design, create, and maintain a performant and stable Kinesis Data Analytics application\.

## Kinesis Data Analytics Application Resources<a name="how-resources-kda"></a>

Kinesis Data Analytics is an AWS service that creates an environment for hosting your Apache Flink application\. The Kinesis Data Analytics service provides resources using units called **Kinesis Processing Units \(KPUs\)**\.

One KPU represents the following system resources:
+ One CPU core
+ 4 GB of memory, of which one GB is native memory and three GB are heap memory
+ 50 GB of disk space

KPUs run applications in distinct execution units called **tasks** and **subtasks**\. You can think of a subtask as the equivalent of a thread\.

The number of KPUs available to an application is equal to the application's `Parallelism` setting, divided by the application's `ParallelismPerKPU` setting\. 

For more information about application parallelism, see [ScalingProduction readiness](how-scaling.md)\.

## Apache Flink Application Resources<a name="how-resources-flink"></a>

The Apache Flink environment allocates resources for your application using units called **task slots**\. When Kinesis Data Analytics allocates resources for your application, it assigns one or more Apache Flink task slots to a single KPU\. The number of slots assigned to a single KPU is equal to your application's `ParallelismPerKPU` setting\. For more information about task slots, see [ Job Scheduling](https://nightlies.apache.org/flink/flink-docs-release-1.15/internals/job_scheduling.html) in the [Apache Flink documentation](https://nightlies.apache.org/flink/flink-docs-release-1.15/)\.

### Operator Parallelism<a name="how-resources-flink-operatorparallelism"></a>

You can set the maximum number of subtasks that an operator can use\. This value is called **Operator Parallelism**\. By default, the parallelism of each operator in your application is equal to the application's parallelism\. This means that by default, each operator in your application can use all of the available subtasks in the application if needed\.

You can set the parallelism of the operators in your application using the `setParallelism` method\. Using this method, you can control the number of subtasks each operator can use at one time\.

For more information about operator chaining, see [ Task chaining and resource groups](https://nightlies.apache.org/flink/flink-docs-release-1.15/dev/stream/operators/#task-chaining-and-resource-groups) in the [Apache Flink Documentation](https://nightlies.apache.org/flink/flink-docs-release-1.15/)\.

### Operator Chaining<a name="how-resources-flink-operatorchaining"></a>

Normally, each operator uses a separate subtask to execute, but if several operators always execute in sequence, the runtime can assign them all to the same task\. This process is called **Operator Chaining**\.

Several sequential operators can be chained into a single task if they all operate on the same data\. The following are some of the criteria needed for this to be true:
+ The operators do 1\-to\-1 simple forwarding\.
+ The operators all have the same operator parallelism\.

When your application chains operators into a single subtask, it conserves system resources, because the service doesn't need to perform network operations and allocate subtasks for each operator\. To determine if your application is using operator chaining, look at the job graph in the Kinesis Data Analytics console\. Each vertex in the application represents one or more operators\. The graph shows operators that have been chained as a single vertex\.