# Programming Your Kinesis Data Analytics for Python Application<a name="how-python-programming"></a>

You code your Kinesis Data Analytics for Python application using the Apache Flink Python Table API\. The Apache Flink engine translates Python Table API statements \(running in the Python VM\) into Java Table API statements \(running in the Java VM\)\. 

You use the Python Table API by doing the following:
+ Create a reference to the `StreamTableEnvironment`\.
+ Create `table` objects from your source streaming data by executing queries on the `StreamTableEnvironment` reference\.
+ Execute queries on your `table` objects to create output tables\.
+ Write your output tables to your destinations using a `StatementSet`\.

To get started using the Python Table API in Kinesis Data Analytics, see [Getting Started with Amazon Kinesis Data Analytics for Apache Flink for Python](gs-python.md)\.

## Reading and Writing Streaming Data<a name="how-python-programming-readwrite"></a>

To read and write streaming data, you execute SQL queries on the table environment\.

### Creating a Table<a name="how-python-programming-readwrite-createtable"></a>

The following code example demonstrates a user\-defined function that creates a SQL query\. The SQL query creates a table that interacts with a Kinesis stream:

```
def create_table(table_name, stream_name, region, stream_initpos):
   return """ CREATE TABLE {0} (
                `record_id` VARCHAR(64) NOT NULL,
                `event_time` BIGINT NOT NULL,
                `record_number` BIGINT NOT NULL,
                `num_retries` BIGINT NOT NULL,
                `verified` BOOLEAN NOT NULL
              )
              PARTITIONED BY (record_id)
              WITH (
                'connector' = 'kinesis',
                'stream' = '{1}',
                'aws.region' = '{2}',
                'scan.stream.initpos' = '{3}',
                'sink.partitioner-field-delimiter' = ';',
                'sink.producer.collection-max-count' = '100',
                'format' = 'json',
                'json.timestamp-format.standard' = 'ISO-8601'
              ) """.format(table_name, stream_name, region, stream_initpos)
```

### Reading Streaming Data<a name="how-python-programming-readwrite-read"></a>

The following code example demonstrates how to use preceding `CreateTable`SQL query on a table environment reference to read data:

```
   table_env.execute_sql(create_table(input_table, input_stream, input_region, stream_initpos))
```

### Writing Streaming Data<a name="how-python-programming-readwrite-write"></a>

The following code example demonstrates how to use the SQL query from the `CreateTable` example to create an output table reference, and how to use a `StatementSet` to interact with the tables to write data to a destination Kinesis stream:

```
   table_result = table_env.execute_sql("INSERT INTO {0} SELECT * FROM {1}"
                       .format(output_table_name, input_table_name))
```

## Reading Runtime Properties<a name="how-python-programming-properties"></a>

You can use runtime properties to configure your application without changing your application code\.

You specify application properties for your application the same way as with a Kinesis Data Analytics for Java application\. You can specify runtime properties in the following ways:
+ Using the [CreateApplication](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_CreateApplication.html) action\.
+ Using the [UpdateApplication](https://docs.aws.amazon.com/kinesisanalytics/latest/apiv2/API_UpdateApplication.html) action\.
+ Configuring your application by using the console\.

You retrieve application properties in code by reading a json file called `application_properties.json` that the Kinesis Data Analytics runtime creates\.

The following code example demonstrates reading application properties from the `application_properties.json` file:

```
file_path = '/etc/flink/application_properties.json'
   if os.path.isfile(file_path):
       with open(file_path, 'r') as file:
           contents = file.read()
           properties = json.loads(contents)
```

The following user\-defined function code example demonstrates reading a property group from the application properties object: retrieves:

```
def property_map(properties, property_group_id):
   for prop in props:
       if prop["PropertyGroupId"] == property_group_id:
           return prop["PropertyMap"]
```

The following code example demonstrates reading a property called INPUT\_STREAM\_KEY from a property group that the previous example returns:

```
input_stream = input_property_map[INPUT_STREAM_KEY]
```

## Creating your application's code package<a name="how-python-programming-package"></a>

Once you have created your Python application, you bundle your code file and dependencies into a zip file\.

Your zip file must contain a python script with a `main` method, and can optionally contain the following:
+ Additional Python code files
+ User\-defined Java code in JAR files
+ Java libraries in JAR files

**Note**  
Your application zip file must contain all of the dependencies for your application\. You can't reference libraries from other sources for your application\.