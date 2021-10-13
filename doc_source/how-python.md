# Using Python with Kinesis Data Analytics<a name="how-python"></a>

Apache Flink version 1\.13\.2 includes support for creating applications using Python version 3\.8, using the [PyFlink](https://flink.apache.org/2020/04/09/pyflink-udf-support-flink.html) library\. You create a Kinesis Data Analytics application using Python by doing the following:
+ Create your Python application code as a text file with a `main` method\.
+ Bundle your application code file and any Python or Java dependencies into a zip file, and upload it to an Amazon S3 bucket\.
+ Create your Kinesis Data Analytics application, specifying your Amazon S3 code location, application properties, and application settings\.

At a high level, the Python Table API is a wrapper around the Java Table API\. For information about the Python Table API, see [ Intro to the Python Table API](https://ci.apache.org/projects/flink/flink-docs-release-1.13/dev/python/table-api-users-guide/intro_to_table_api.html) in the [ Apache Flink Documentation](https://ci.apache.org/projects/flink/flink-docs-release-1.13/)\.