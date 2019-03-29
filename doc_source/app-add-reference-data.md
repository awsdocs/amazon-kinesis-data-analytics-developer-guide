# Example: Adding Reference Data to a Kinesis Data Analytics Application<a name="app-add-reference-data"></a>

In this exercise, you add reference data to an existing Amazon Kinesis data analytics application\. For information about reference data, see the following topics:
+ [Amazon Kinesis Data Analytics for SQL Applications: How It Works](how-it-works.md)
+ [Configuring Application Input](how-it-works-input.md)

In this exercise, you add reference data to the application you created in the Kinesis Data Analytics [Getting Started](https://docs.aws.amazon.com/kinesisanalytics/latest/dev/get-started-exercise.html) exercise\. The reference data provides the company name for each ticker symbol; for example:

```
Ticker, Company
AMZN,Amazon
ASD, SomeCompanyA
MMB, SomeCompanyB
WAS,  SomeCompanyC
```

First, complete the steps in the [Getting Started](https://docs.aws.amazon.com/kinesisanalytics/latest/dev/get-started-exercise.html) exercise to create a starter application\. Then follow these steps to set up and add reference data to your application:

1. **Prepare the data**
   + Store the preceding reference data as an object in Amazon Simple Storage Service \(Amazon S3\)\.
   + Create an IAM role that Kinesis Data Analytics can assume to read the Amazon S3 object on your behalf\.

1. **Add the reference data source to your application\. **

   Kinesis Data Analytics reads the Amazon S3 object and creates an in\-application reference table that you can query in your application code\.

1. **Test the code\.**

   In your application code, you write a join query to join the in\-application stream with the in\-application reference table, to get the company name for each ticker symbol\.

**Topics**
+ [Step 1: Prepare](#add-refdata-prepare)
+ [Step 2: Add the Reference Data Source to the Application Configuration](#add-refdata-create-iamrole)
+ [Step 3: Test: Query the In\-Application Reference Table](#add-refdata-test)

## Step 1: Prepare<a name="add-refdata-prepare"></a>

In this section, you store sample reference data as an object in an Amazon S3 bucket\. You also create an IAM role that Kinesis Data Analytics can assume to read the object on your behalf\.

### Store Reference Data as an Amazon S3 Object<a name="prepare-create-s3object"></a>

In this step, you store the sample reference data as an Amazon S3 object\.

1. Open a text editor, add the following data, and save the file as `TickerReference.csv`\. 

   ```
   Ticker, Company
   AMZN,Amazon
   ASD, SomeCompanyA
   MMB, SomeCompanyB
   WAS,  SomeCompanyC
   ```

1. Upload the `TickerReference.csv` file to your S3 bucket\. For instructions, see [Uploading Objects into Amazon S3](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/UploadingObjectsintoAmazonS3.html) in the *Amazon Simple Storage Service Console User Guide*\.

### Create an IAM Role<a name="prepare-create-iamrole"></a>

Next, create an IAM role that Kinesis Data Analytics can assume and read the Amazon S3 object\.

1. In AWS Identity and Access Management \(IAM\), create an IAM role named **KinesisAnalytics\-ReadS3Object**\. To create the role, follow the instructions in [Creating a Role for an AWS Service \(AWS Management Console\)](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_create_for-service.html#roles-creatingrole-service-console) in the *IAM User Guide*\.

   On the IAM console, specify the following:
   + For **Select Role Type**, choose **AWS Lambda**\. After creating the role, you will change the trust policy to allow Kinesis Data Analytics \(not AWS Lambda\) to assume the role\.
   + Do not attach any policy on the **Attach Policy** page\.

1. Update the IAM role policies:

   1. On the IAM console, choose the role that you created\.

   1. On the **Trust Relationships** tab, update the trust policy to grant Kinesis Data Analytics permissions to assume the role\. The trust policy is shown following:

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

   1. On the **Permissions** tab, attach an AWS managed policy called **AmazonS3ReadOnlyAccess**\. This grants the role permissions to read an Amazon S3 object\. This policy is shown following:

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

## Step 2: Add the Reference Data Source to the Application Configuration<a name="add-refdata-create-iamrole"></a>

In this step, you add a reference data source to your application configuration\. To begin, you need the following information: 
+ Your S3 bucket name and object key name
+ The IAM role Amazon Resource Name \(ARN\)

1. In the main page for the application, choose **Connect reference data**\.

1. In the **Connect reference data source** page, choose the Amazon S3 bucket containing your reference data object, and enter the object's key name\.

1. Enter **CompanyName** for the **In\-application reference table name**\.

1. In the **Access to chosen resources** section, choose **Choose from IAM roles that Kinesis Analytics can assume**, and choose the **KinesisAnalytics\-ReadS3Object** IAM role you created in the previous section\.

1. Choose **Discover schema**\. The console detects two columns in the reference data\.

1. Choose **Save and close**\.

## Step 3: Test: Query the In\-Application Reference Table<a name="add-refdata-test"></a>

You can now query the in\-application reference table, `CompanyName`\. You can use the reference information to enrich your application by joining the ticker price data with the reference table\. The result shows the company name\.

1. Replace your application code with the following\. The query joins the in\-application input stream with the in\-application reference table\. The application code writes the results to another in\-application stream, `DESTINATION_SQL_STREAM`\. 

   ```
   CREATE OR REPLACE STREAM "DESTINATION_SQL_STREAM" (ticker_symbol VARCHAR(4), "Company" varchar(20), sector VARCHAR(12), change DOUBLE, price DOUBLE);
   
   CREATE OR REPLACE PUMP "STREAM_PUMP" AS INSERT INTO "DESTINATION_SQL_STREAM"
      SELECT STREAM ticker_symbol, "c"."Company", sector, change, price
      FROM "SOURCE_SQL_STREAM_001" LEFT JOIN "CompanyName" as "c"
      ON "SOURCE_SQL_STREAM_001".ticker_symbol = "c"."Ticker";
   ```

1. Verify that the application output appears in the **SQLResults** tab\. Make sure that some of the rows show company names \(your sample reference data does not have all company names\)\.