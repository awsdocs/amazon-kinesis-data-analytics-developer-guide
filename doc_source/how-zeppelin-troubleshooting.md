# Troubleshooting<a name="how-zeppelin-troubleshooting"></a>

This section contains troubleshooting information for Studio notebooks\.

## Stopping a stuck application<a name="how-zeppelin-troubleshooting-stopping"></a>

To stop an application that is stuck in a transient state, call the [StopApplication](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_StopApplication.html) action with the `Force` parameter set to `true`\. For more information, see [Running Applications](https://docs.aws.amazon.com/kinesisanalytics/latest/java/how-running-apps.html) in the [Kinesis Data Analytics Developer Guide](https://docs.aws.amazon.com/kinesisanalytics/latest/java/)\.

## Canceling jobs<a name="how-notbook-canceling-jobs"></a>

This section shows you how to cancel Apache Flink jobs that you can't get to from Apache Zeppelin\. If you want to cancel such a job, go to the Apache Flink dashboard, copy the job ID, then use it in one of the following examples\.

To cancel a single job:

```
%flink.pyflink
import requests

requests.patch("https://zeppelin-flink:8082/jobs/[job_id]", verify=False)
```

To cancel all running jobs:

```
%flink.pyflink
import requests

r = requests.get("https://zeppelin-flink:8082/jobs", verify=False)
jobs = r.json()['jobs']

for job in jobs:
    if (job["status"] == "RUNNING"):
        print(requests.patch("https://zeppelin-flink:8082/jobs/{}".format(job["id"]), verify=False))
```

To cancel all jobs:

```
%flink.pyflink
import requests

r = requests.get("https://zeppelin-flink:8082/jobs", verify=False)
jobs = r.json()['jobs']

for job in jobs:
    requests.patch("https://zeppelin-flink:8082/jobs/{}".format(job["id"]), verify=False)
```

## Restarting the Apache Flink interpreter<a name="how-notbook-restarting-interpreter"></a>

To restart the Apache Flink interpreter within your Studio notebook

1. Choose **Configuration** near the top right corner of the screen\.

1. Choose **Interpreter**\.

1. Choose **restart** and then **OK**\.