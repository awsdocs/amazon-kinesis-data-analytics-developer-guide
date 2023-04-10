# Working with AWS Glue<a name="how-zeppelin-glue"></a>

Your Studio notebook stores and gets information about its data sources and sinks from AWS Glue\. When you create your Studio notebook, you specify the AWS Glue database that contains your connection information\. When you access your data sources and sinks, you specify AWS Glue tables contained in the database\. Your AWS Glue tables provide access to the AWS Glue connections that define the locations, schemas, and parameters of your data sources and destinations\.

Studio notebooks use table properties to store application\-specific data\. For more information, see [Table properties](how-zeppelin-glue-properties.md)\.

For an example of how to set up a AWS Glue connection, database, and table for use with Studio notebooks, see [Create an AWS Glue Database](example-notebook.md#example-notebook-glue) in the [Creating a Studio notebook Tutorial](example-notebook.md) tutorial\.