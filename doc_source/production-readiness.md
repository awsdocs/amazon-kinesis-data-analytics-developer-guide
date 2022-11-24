# Production readiness<a name="production-readiness"></a>

This is a collection of important aspects of running production applications on Kinesis Data Analytics\. It's not an exhaustive list, but rather the bare minimum of what you should pay attention to before putting an application into production\.

## Load testing applications<a name="load-testing-applications"></a>

Some problems with applications only manifest under heavy load\. We have seen cases where applications seemed healthy and an operational event substantially amplified the load on the application\. This can happen completely independent of the application itself: If the data source or the data sink is unavailable for a couple of hours, the Flink application cannot make progress\. Once that issue is fixed there is a backlog of unprocessed data has accumulated that can completely exhaust the available resources\. The load can then amplify bugs or performance issues that have not been emerging before\.

It is therefore essential to run proper load tests for production applications\. Questions that should be answered during those load tests include:
+ Is the application stable under sustained high load?
+ Can the application still take a savepoint under peak load?
+ How long does it take to process a backlog of 1 hour? And how long for 24 hours \(depending on the max retention of the data in the stream\)?
+ Does the throughput of the application increase when the application is scaled?

When consuming from a data stream, these scenarios can be simulated by producing into the stream for some time\. Then start the application and have it consume data from the beginning of time, e\.g\., use a start position of `TRIM_HORIZON` in the case of a Kinesis Data Stream\.

## Setting an explicit max parallelism for each of your operators within your DAG<a name="explicit-max-parallelism"></a>

This should be a function of your `total parallelism` for the application\. An example would be if your total parallelism is 10, an individual operator could be set to half of the total parallelism, such as:

```
.setParallelism(MAX_PARALLELISM/2)
```

 where `MAX_PARALLELISM` is a variable equal to the result of `env.getParallelism()`\. 

## Set a UUID for all operators<a name="set-uuid"></a>

A UUID is used in the operation in which Flink maps a savepoint back to an individual operator\. Setting a specific UUID for each operator gives a stable mapping for the savepoint process to restore\.

```
.map(...).uid("my-map-function")
```

For more information, see [Production Readiness Checklist](https://nightlies.apache.org/flink/flink-docs-release-1.13/docs/ops/production_ready/)\.