# Using the Schema Discovery Feature on Static Data<a name="sch-dis-ref"></a>

The schema discovery feature can generate a schema from either the data in a stream or data in a static file that is stored in an Amazon S3 bucket\. Suppose that you want to generate a schema for a Kinesis Data Analytics application for reference purposes or when live streaming data isn't available\. You can use the schema discovery feature on a static file that contains a sample of the data in the expected format of your streaming or reference data\. Kinesis Data Analytics can run schema discovery on sample data from a JSON or CSV file that's stored in an Amazon S3 bucket\. Using schema discovery on a data file uses either the console, or the [DiscoverInputSchema](API_DiscoverInputSchema.md) API with the `S3Configuration` parameter specified\.

## Running Schema Discovery Using the Console<a name="sch-dis-ref-console"></a>

To run discovery on a static file using the console, do the following:

1. Add a reference data object to an S3 bucket\.

1. Choose **Connect reference data** in the application's main page in the Kinesis Data Analytics console\.

1. Provide the bucket, path and IAM role data for accessing the Amazon S3 object containing the reference data\.

1. Choose **Discover schema**\.

For more information on how to add reference data and discover schema in the console, see [Example: Adding Reference Data to a Kinesis Data Analytics Application](app-add-reference-data.md)\.

## Running Schema Discovery Using the API<a name="sch-dis-ref-api"></a>

To run discovery on a static file using the API, you provide the API with an `S3Configuration` structure with the following information:
+ `BucketARN`: The Amazon Resource Name \(ARN\) of the Amazon S3 bucket that contains the file\. For the format of an Amazon S3 bucket ARN, see [Amazon Resource Names \(ARNs\) and AWS Service Namespaces: Amazon Simple Storage Service \(Amazon S3\)](https://docs.aws.amazon.com/general/latest/gr/aws-arns-and-namespaces.html#arn-syntax-s3)\.
+ `RoleARN`: The ARN of an IAM role with the `AmazonS3ReadOnlyAccess` policy\. For information about how to add a policy to a role, see [Modifying a Role](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_manage_modify.html)\.
+ `FileKey`: The file name of the object\.

**To generate a schema from an Amazon S3 object using the `DiscoverInputSchema` API**

1. Make sure that you have the AWS CLI set up\. For more information, see [Step 2: Set Up the AWS Command Line Interface \(AWS CLI\)](setup-awscli.md) in the Getting Started section\.

1. Create a file named `data.csv` with the following contents:

   ```
   year,month,state,producer_type,energy_source,units,consumption
   2001,1,AK,TotalElectricPowerIndustry,Coal,ShortTons,47615
   2001,1,AK,ElectricGeneratorsElectricUtilities,Coal,ShortTons,16535
   2001,1,AK,CombinedHeatandPowerElectricPower,Coal,ShortTons,22890
   2001,1,AL,TotalElectricPowerIndustry,Coal,ShortTons,3020601
   2001,1,AL,ElectricGeneratorsElectricUtilities,Coal,ShortTons,2987681
   ```

1. Sign in to the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. Create an Amazon S3 bucket and upload the `data.csv` file you created\. Note the ARN of the created bucket\. For information about creating an Amazon S3 bucket and uploading a file, see [Getting Started with Amazon Simple Storage Service](https://docs.aws.amazon.com/AmazonS3/latest/gsg/GetStartedWithS3.html)\. 

1. Open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\. Create a role with the `AmazonS3ReadOnlyAccess` policy\. Note the ARN of the new role\. For information about creating a role, see [Creating a Role to Delegate Permissions to an AWS Service](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_create_for-service.html)\. For information about how to add a policy to a role, see [Modifying a Role](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_manage_modify.html)\.

1. Run the following `DiscoverInputSchema` command in the AWS CLI, substituting the ARNs for your Amazon S3 bucket and IAM role:

   ```
   $aws kinesisanalytics discover-input-schema --s3-configuration '{ "RoleARN": "arn:aws:iam::123456789012:role/service-role/your-IAM-role", "BucketARN": "arn:aws:s3:::your-bucket-name", "FileKey": "data.csv" }' 
   ```

1. The response looks similar to the following:

   ```
   {
       "InputSchema": {
           "RecordEncoding": "UTF-8",
           "RecordColumns": [
               {
                   "SqlType": "INTEGER",
                   "Name": "COL_year"
               },
               {
                   "SqlType": "INTEGER",
                   "Name": "COL_month"
               },
               {
                   "SqlType": "VARCHAR(4)",
                   "Name": "state"
               },
               {
                   "SqlType": "VARCHAR(64)",
                   "Name": "producer_type"
               },
               {
                   "SqlType": "VARCHAR(4)",
                   "Name": "energy_source"
               },
               {
                   "SqlType": "VARCHAR(16)",
                   "Name": "units"
               },
               {
                   "SqlType": "INTEGER",
                   "Name": "consumption"
               }
           ],
           "RecordFormat": {
               "RecordFormatType": "CSV",
               "MappingParameters": {
                   "CSVMappingParameters": {
                       "RecordRowDelimiter": "\r\n",
                       "RecordColumnDelimiter": ","
                   }
               }
           }
       },
       "RawInputRecords": [
           "year,month,state,producer_type,energy_source,units,consumption\r\n2001,1,AK,TotalElectricPowerIndustry,Coal,ShortTons,47615\r\n2001,1,AK,ElectricGeneratorsElectricUtilities,Coal,ShortTons,16535\r\n2001,1,AK,CombinedHeatandPowerElectricPower,Coal,ShortTons,22890\r\n2001,1,AL,TotalElectricPowerIndustry,Coal,ShortTons,3020601\r\n2001,1,AL,ElectricGeneratorsElectricUtilities,Coal,ShortTons,2987681"
       ],
       "ParsedInputRecords": [
           [
               null,
               null,
               "state",
               "producer_type",
               "energy_source",
               "units",
               null
           ],
           [
               "2001",
               "1",
               "AK",
               "TotalElectricPowerIndustry",
               "Coal",
               "ShortTons",
               "47615"
           ],
           [
               "2001",
               "1",
               "AK",
               "ElectricGeneratorsElectricUtilities",
               "Coal",
               "ShortTons",
               "16535"
           ],
           [
               "2001",
               "1",
               "AK",
               "CombinedHeatandPowerElectricPower",
               "Coal",
               "ShortTons",
               "22890"
           ],
           [
               "2001",
               "1",
               "AL",
               "TotalElectricPowerIndustry",
               "Coal",
               "ShortTons",
               "3020601"
           ],
           [
               "2001",
               "1",
               "AL",
               "ElectricGeneratorsElectricUtilities",
               "Coal",
               "ShortTons",
               "2987681"
           ]
       ]
   }
   ```