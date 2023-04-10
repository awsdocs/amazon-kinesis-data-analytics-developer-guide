# Deploying as an application with durable state<a name="how-notebook-durable"></a>

You can build your code and export it to Amazon S3\. You can promote the code that you wrote in your note to a continuously running stream processing application\. There are two modes of running an Apache Flink application on Kinesis Data Analytics: With a Studio notebook, you have the ability to develop your code interactively, view results of your code in real time, and visualize it within your note\. After you deploy a note to run in streaming mode, Kinesis Data Analytics creates an application for you that runs continuously, reads data from your sources, writes to your destinations, maintains long\-running application state, and autoscales automatically based on the throughput of your source streams\. 

**Note**  
The S3 bucket to which you export your application code must be in the same Region as your Studio notebook\.

You can only deploy a note from your Studio notebook if it meets the following criteria:
+ Paragraphs must be ordered sequentially\. When you deploy your application, all paragraphs within a note will be executed sequentially \(left\-to\-right, top\-to\-bottom\) as they appear in your note\. You can check this order by choosing **Run All Paragraphs** in your note\.
+ Your code is a combination of Python and SQL or Scala and SQL\. We do not support Python and Scala together at this time for deploy\-as\-application\.
+ Your note should have only the following interpreters: `%flink`, `%flink.ssql`, `%flink.pyflink`, `%flink.ipyflink`, `%md`\.
+ The use of the [Zeppelin context](https://zeppelin.apache.org/docs/0.9.0/usage/other_features/zeppelin_context.html) object `z` is not supported\. Methods that return nothing will do nothing except log a warning\. Other methods will raise Python exceptions or fail to compile in Scala\.
+ A note must result in a single Apache Flink job\. 
+ Notes with [dynamic forms](https://zeppelin.apache.org/docs/0.9.0/usage/dynamic_form/intro.html) are unsupported for deploying as an application\.
+ %md \([Markdown](https://zeppelin.apache.org/docs/0.9.0/interpreter/markdown.html)\) paragraphs will be skipped in deploying as an application, as these are expected to contain human\-readable documentation that is unsuitable for running as part of the resulting application\.
+ Paragraphs disabled for running within Zeppelin will be skipped in deploying as an application\. Even if a disabled paragraph uses an incompatible interpreter, for example, `%flink.ipyflink` in a note with `%flink` `and %flink.ssql` interpreters, it will be skipped while deploying the note as an application, and will not result in an error\.
+ There must be at least one paragraph present with source code \(Flink SQL, PyFlink or Flink Scala\) that is enabled for running for the application deployment to succeed\.
+ Setting parallelism in the interpreter directive within a paragraph \(e\.g\. `%flink.ssql(parallelism=32)`\) will be ignored in applications deployed from a note\. Instead, you can update the deployed application through the AWS Management Console, AWS Command Line Interface or AWS API to change the Parallelism and/or ParallelismPerKPU settings according to the level of parallelism your application requires, or you can enable autoscaling for your deployed application\.

## Scala/Python criteria<a name="how-notebook-durable-scala"></a>
+ Your Scala or Python code can't use a `BatchExecutionEnvironment` or `BatchTableEnvironment` \(`benv`, `btenv`, `btenv_2` for Scala; `b_env`, `bt_env`, `bt_env_2` for Python\)\.
+ In your Scala or Python code, use the [Blink planner](https://nightlies.apache.org/flink/flink-docs-release-1.15/dev/table/#dependency-structure) \(`senv`, `stenv` for Scala; `s_env`, `st_env` for Python\) and not the older "Flink" planner \(`stenv_2` for Scala, `st_env_2` for Python\)\. The Apache Flink project recommends the use of the Blink planner for production use cases, and this is the default planner in Zeppelin and in Flink\.
+ Your Python paragraphs must not use [shell invocations/assignments](https://ipython.readthedocs.io/en/stable/interactive/python-ipython-diff.html#shell-assignment) using `!` or [IPython magic commands](https://ipython.readthedocs.io/en/stable/interactive/magics.html) like `%timeit` or `%conda` in notes meant to be deployed as applications\.
+ You can't use Scala case classes as parameters of functions passed to higher\-order dataflow operators like `map` and `filter`\. For information about Scala case classes, see [CASE CLASSES](https://docs.scala-lang.org/overviews/scala-book/case-classes.html) in the Scala documentation\.

## SQL criteria<a name="how-notebook-durable-sql"></a>
+ Simple SELECT statements are not permitted, as there’s nowhere equivalent to a paragraph’s output section where the data can be delivered\.
+ In any given paragraph, DDL statements \(`USE`, `CREATE`, `ALTER`, `DROP`, `SET`, `RESET`\) must precede DML \(`INSERT`\) statements\. This is because DML statements in a paragraph must be submitted together as a single Flink job\.
+ There should be at most one paragraph that has DML statements in it\. This is because, for the deploy\-as\-application feature, we only support submitting a single job to Flink\.

For more information and an example, see [ Translate, redact and analyze streaming data using SQL functions with Amazon Kinesis Data Analytics, Amazon Translate, and Amazon Comprehend](https://aws.amazon.com/blogs/machine-learning/translate-redact-and-analyze-streaming-data-using-sql-functions-with-amazon-kinesis-data-analytics-amazon-translate-and-amazon-comprehend/)\.