# Example: Adding Reference Data to an Amazon Kinesis Data Analytics Application<a name="app-add-reference-data"></a>


+ [Step 1: Prepare](#add-refdata-prepare)
+ [Step 2: Add Reference Data Source to the Application Configuration](#add-refdata-create-iamrole)
+ [Step 3: Test: Query the In\-Application Reference Table](#add-refdata-test)

In this exercise, you add reference data to an existing Amazon Kinesis Data Analytics application\. For information about reference data, see the following topics:

+ [Amazon Kinesis Data Analytics: How It Works](how-it-works.md)

+ [Configuring Application Input](how-it-works-input.md)

In this exercise you add reference data to the application you created in the getting started exercise\. The reference data provides company name for each ticker symbol\. For example,

```
Ticker, Company
AMZN,Amazon
ASD, SomeCompanyA
MMB, SomeCompanyB
WAS,  SomeCompanyC
```

First complete the [Getting Started Exercise](http://docs.aws.amazon.com/kinesisanalytics/latest/dev/get-started-exercise.html)\. Then you do the following to set up and add reference data to your application\. 

1. Prepare

   + Store preceding reference data as an object in your S3 bucket\.

   + Create an IAM role, that Amazon Kinesis Data Analytics can assume to read the S3 object on your behalf\.

1. Add the reference data source to your application\. Amazon Kinesis Data Analytics reads the S3 object and create an in\-application reference table that you can query in your application code\.

1. Test\. In your application code you will write a join query to join the in\-application stream with the in\-application reference table, to get company name for each ticker symbol\.

**Note**  
Amazon Kinesis Data Analytics console does not support managing reference data sources for your applications\. In this exercise, you use the AWS CLI to add reference data source to your application\. If you haven't already done so, [set up the AWS CLI](http://docs.aws.amazon.com/kinesisanalytics/latest/dev/setup-awscli.html)\.

## Step 1: Prepare<a name="add-refdata-prepare"></a>

In this section, you store sample reference data as an object in your S3 bucket\. You also create an IAM role that Amazon Kinesis Data Analytics can assume to read the object on your behalf\.

### Prepare: Store Reference Data as S3 Object<a name="prepare-create-s3object"></a>

Store sample reference data as S3 object\.

1. Open a text editor, type the following data, and save the file as `TickerReference.csv`\. 

   ```
   Ticker, Company
   AMZN,Amazon
   ASD, SomeCompanyA
   MMB, SomeCompanyB
   WAS,  SomeCompanyC
   ```

1. Upload the `TickerReference.csv` file to your S3 bucket\. For instructions, see [Uploading Objects into Amazon S3](http://docs.aws.amazon.com/AmazonS3/latest/user-guide/UploadingObjectsintoAmazonS3.html) in the *Amazon Simple Storage Service Console User Guide*\.

### Prepare: Create an IAM Role<a name="prepare-create-iamrole"></a>

Create an IAM role\. Follow the procedure to create an IAM role that Amazon Kinesis Data Analytics can assume and read the S3 object\.

1. Create an IAM role called **KinesisAnalytics\-ReadS3Object**\. In the IAM console, you specify the following when you create a role:

   + Choose **AWS Lambda** on the **Select Role Type**\. After creating the role, you will change the trust policy to allow Amazon Kinesis Data Analytics to assume the role \(not AWS Lambda\)\.

   + Do not attach any policy on the **Attach Policy** page\.

   For instructions, see [Creating a Role for an AWS Service \(AWS Management Console\)](http://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_create_for-service.html#roles-creatingrole-service-console) in the *IAM User Guide*\.

1. Update the IAM role policies\.

   1. In the IAM console, select the role you created\.

   1. On the **Trust Relationships** tab, update the trust policy to allow Amazon Kinesis Data Analytics permissions to assume the role\. The trust policy is shown following:

      ```
      {
        "Version": "2012-10-17",
        "Statement": [
          {
            "Effect": "Allow",
            "Principal": {
              "Service": "kinesisanalytics.amazonaws.com"
            },
            "Action": "sts:AssumeRole"
          }
        ]
      }
      ```

   1. On the **Permissions** tab, attach an AWS managed policy called **AmazonS3ReadOnlyAccess**\. This grants the role permissions to read an S3 object\. The policy is shown following for your information:

      ```
      {
        "Version": "2012-10-17",
        "Statement": [
          {
            "Effect": "Allow",
            "Action": [
              "s3:Get*",
              "s3:List*"
            ],
            "Resource": "*"
          }
        ]
      }
      ```

## Step 2: Add Reference Data Source to the Application Configuration<a name="add-refdata-create-iamrole"></a>

In this section you add reference data source to your application configuration\. You will need the following information: 

+ Your Amazon Kinesis Data Analytics application name and current application version ID

+ S3 bucket name and object key name

+ IAM role ARN

Now, you now use the AWS CLI to complete the step:

1. Run the `describe-application` to get the application description, as shown following:

   ```
   $  aws kinesisanalytics describe-application  \
   --region us-east-1 \
   --application-name application-name
   ```

1. Note the current application version ID\.

   Each time you make changes to your application, the current version is updated\. So you need to make sure you have the current application version ID\.

1. Use the following JSON to add the reference data source:

   ```
   {
      "TableName":"CompanyName",
      "S3ReferenceDataSource":{
         "BucketARN":"arn:aws:s3:::bucket-name",
         "FileKey":"TickerReference.csv",
         "ReferenceRoleARN":"arn:aws:iam::aws-account-id:role/IAM-role-name"
      },
      "ReferenceSchema":{
         "RecordFormat":{
            "RecordFormatType":"CSV",
            "MappingParameters":{
               "CSVMappingParameters":{
                  "RecordRowDelimiter":"\n",
                  "RecordColumnDelimiter":","
               }
            }
         },
         "RecordEncoding":"UTF-8",
         "RecordColumns":[
            {
               "Name":"Ticker",
               "SqlType":"VARCHAR(64)"
            },
            {
               "Name":"Company",
               "SqlType":"VARCHAR(64)"
            }
         ]
      }
   }
   ```

   Run the `add-application-reference-data-source` command using the preceding reference data configuration information\. You need to provide your bucket name, object key name, IAM role name, and AWS account ID\.

   ```
   $  aws kinesisanalytics add-application-reference-data-source  \
   --endpoint https://kinesisanalytics.aws-region.amazonaws.com \
   --region us-east-1 \
   --application-name DemoStreamBasedGettingStarted \
   --debug \
   --reference-data-source '{"TableName":"CompanyName","S3ReferenceDataSource":{"BucketARN":"arn:aws:s3:::bucket-name","FileKey":"TickerReference.csv",
   "ReferenceRoleARN":"arn:aws:iam::aws-account-id:role/IAM-role-name"},"ReferenceSchema":{ "RecordFormat":{"RecordFormatType":"CSV", "MappingParameters":{"CSVMappingParameters":{"RecordRowDelimiter":"\n","RecordColumnDelimiter":","} }},"RecordEncoding":"UTF-8","RecordColumns":[{"Name":"Ticker","SqlType":"VARCHAR(64)"},{ "Name":"Company","SqlType":"VARCHAR(64)"}]}}' \
   --current-application-version-id 10
   ```

1. Verify that the reference data was added to the application by getting the application description using the `describe-application` operation\.

## Step 3: Test: Query the In\-Application Reference Table<a name="add-refdata-test"></a>

You can now query the in\-application reference table, `CompanyName`\. You can use the reference information to enrich your application by joining the ticker price data with the reference table, and then the result shows the company name\.

1. Replace your application code by the following\. The query joins the in\-application input stream with the in\-application reference table\. The application code writes the results to another in\-application stream, DESTINATION\_SQL\_STREAM\. 

   ```
   CREATE OR REPLACE STREAM "DESTINATION_SQL_STREAM" (ticker_symbol VARCHAR(4), "Company" varchar(20), sector VARCHAR(12), change DOUBLE, price DOUBLE);
   
   CREATE OR REPLACE PUMP "STREAM_PUMP" AS INSERT INTO "DESTINATION_SQL_STREAM"
      SELECT STREAM ticker_symbol, "c"."Company", sector, change, price
      FROM "SOURCE_SQL_STREAM_001" LEFT JOIN "CompanyName" as "c"
      ON "SOURCE_SQL_STREAM_001".ticker_symbol = "c"."Ticker";
   ```

1. Verify that the application output appears in the **SQLResults** tab\. Make sure some of the rows show company names \(your sample reference data does not have all company names\)\.