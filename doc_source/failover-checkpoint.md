# Delivery Model for Persisting Application Output to an External Destination<a name="failover-checkpoint"></a>

Amazon Kinesis Data Analytics uses an "at least once" delivery model for application output to the configured destinations\. When an application is running, Amazon Kinesis Data Analytics takes internal checkpoints, which are points in time when output records were delivered to the destinations and there is no data loss\. The service uses the checkpoints as needed to ensure that your application output is delivered at least once to the configured destinations\.

In a normal situation, your application processes incoming data continuously, and Kinesis Data Analytics writes the output to the configured destinations such as a Kinesis stream or a Kinesis data delivery stream\.

However, your application can be interrupted, either by your choice or by some application configuration change that causes an interruption or failure, such as: 

+ You might choose to stop your application and restart it later\.

   

+ You delete the IAM role that Amazon Kinesis Data Analytics needs to write your application output to the configured destination\. Without the IAM role, Kinesis Data Analytics does not have any permissions to write to the external destination on your behalf\.

   

+ Network outage or other internal service failures causing your application to stop running momentarily\. 

When your application starts working again, Kinesis Data Analytics ensures that it continues to process and write output from a point before or equal to when the failure occurred, so that it does not miss delivering any of your application output to the configured destinations\. 

If you configured multiple destinations from the same in\-application stream, after the application recovers from failure, Kinesis Data Analytics resumes persisting output to the configured destinations from the last record that was delivered to the slowest destination\.  This might result in the same output record delivered more than once to other destinations\. In this case, you need to handle potential duplications in the destination externally\. 