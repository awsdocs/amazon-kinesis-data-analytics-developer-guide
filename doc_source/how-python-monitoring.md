# Monitoring Your Python Kinesis Data Analytics Application<a name="how-python-monitoring"></a>

You use your application's CloudWatch log to monitor your Python Kinesis Data Analytics application\.

Kinesis Data Analytics logs the following messages for Python applications:
+ Messages written to the console using `print()` in the application's `main` method\.
+ Messages sent in user\-defined functions using the `logging` package\. The following code example demonstrates writing to the application log from a user\-defined function:

  ```
  import logging
  
  @udf(input_types=[DataTypes.BIGINT()], result_type=DataTypes.BIGINT())
  def doNothingUdf(i):
      logging.info("Got {} in the doNothingUdf".format(str(i)))
      return i
  ```
+ Error messages thrown by the application\.

  If the application throws an exception in the `main` function, it will appear in your application's logs\.

  The following example demonstrates a log entry for an exception thrown from Python code:

  ```
  2021-03-15 16:21:20.000   --------------------------- Python Process Started --------------------------
  2021-03-15 16:21:21.000   Traceback (most recent call last):
  2021-03-15 16:21:21.000   "  File ""/tmp/flink-web-6118109b-1cd2-439c-9dcd-218874197fa9/flink-web-upload/4390b233-75cb-4205-a532-441a2de83db3_code/PythonKinesisSink/PythonUdfUndeclared.py"", line 101, in <module>"
  2021-03-15 16:21:21.000       main()
  2021-03-15 16:21:21.000   "  File ""/tmp/flink-web-6118109b-1cd2-439c-9dcd-218874197fa9/flink-web-upload/4390b233-75cb-4205-a532-441a2de83db3_code/PythonKinesisSink/PythonUdfUndeclared.py"", line 54, in main"
  2021-03-15 16:21:21.000   "    table_env.register_function(""doNothingUdf"", doNothingUdf)"
  2021-03-15 16:21:21.000   NameError: name 'doNothingUdf' is not defined
  2021-03-15 16:21:21.000   --------------------------- Python Process Exited ---------------------------
  2021-03-15 16:21:21.000   Run python process failed
  2021-03-15 16:21:21.000   Error occurred when trying to start the job
  ```

**Note**  
Due to performance issues, we recommend that you only use custom log messages during application development\. 

## Querying Logs with CloudWatch Insights<a name="how-python-monitoring-insights"></a>

The following CloudWatch Insights query searches for logs created by the Python entrypoint while executing the main function of your application:

```
fields @timestamp, message
| sort @timestamp asc
| filter logger like /PythonDriver/
| limit 1000
```