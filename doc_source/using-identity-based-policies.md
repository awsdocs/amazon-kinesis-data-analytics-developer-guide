# Using Identity\-Based Policies \(IAM Policies\) for Amazon Kinesis Data Analytics<a name="using-identity-based-policies"></a>

The following are examples of identity\-based policies that demonstrate how an account administrator can attach permissions policies to IAM identities \(that is, users, groups, and roles\) and grant permissions to perform operations on Amazon Kinesis Data Analytics resources\.

**Important**  
We recommend that you first review the introductory topics that explain the basic concepts and options available to manage access to your Amazon Kinesis Data Analytics resources\. For more information, see [Overview of Managing Access Permissions to Your Amazon Kinesis Data Analytics Resources](access-control-overview.md)\. 

**Topics**
+ [Permissions Required to Use the Amazon Kinesis Data Analytics Console](#console-permissions)
+ [AWS Managed \(Predefined\) Policies for Amazon Kinesis Data Analytics](#access-policy-aws-managed-policies)
+ [Customer Managed Policy Examples](#access-policy-customer-managed-examples)

The following shows an example of a permissions policy\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "Stmt1473028104000",
            "Effect": "Allow",
            "Action": [
                "kinesisanalytics:CreateApplication"
            ],
            "Resource": [
                "*"
            ]
        }
    ]
}
```

The policy has one statement:
+ The first statement grants permissions for one Kinesis Data Analytics action \(`kinesisanalytics:CreateApplication`\) on a resource using the Amazon Resource Name \(ARN\) for the application\. The ARN in this case specifies a wildcard character \(\*\) to indicate that the permission is granted for any resource\.

For a table showing all of the Kinesis Data Analytics API operations and the resources that they apply to, see [Amazon Kinesis Data Analytics API Permissions: Actions, Permissions, and Resources Reference](api-permissions-reference.md)\. 

## Permissions Required to Use the Amazon Kinesis Data Analytics Console<a name="console-permissions"></a>

For a user to work with the Kinesis Data Analytics console, you must grant the necessary permissions\. For example, if you want a user to have permissions to create an application, grant permissions that show them the streaming sources in the account so that the user can configure input and output on the console\.

We recommend the following:
+ Use the AWS managed policies to grant user permissions\. For available policies, see [AWS Managed \(Predefined\) Policies for Amazon Kinesis Data Analytics](#access-policy-aws-managed-policies)\.
+ Create custom policies\. In this case, we recommend that you review the example provided in this section\. For more information, see [Customer Managed Policy Examples](#access-policy-customer-managed-examples)\.

## AWS Managed \(Predefined\) Policies for Amazon Kinesis Data Analytics<a name="access-policy-aws-managed-policies"></a>

AWS addresses many common use cases by providing standalone IAM policies that are created and administered by AWS\. These AWS managed policies grant necessary permissions for common use cases so that you can avoid having to investigate what permissions are needed\. For more information, see [AWS Managed Policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_managed-vs-inline.html#aws-managed-policies) in the *IAM User Guide*\. 

The following AWS managed policies, which you can attach to users in your account, are specific to Amazon Kinesis Data Analytics:
+ **`AmazonKinesisAnalyticsReadOnly`** – Grants permissions for Amazon Kinesis Data Analytics actions that enable a user to list Amazon Kinesis Data Analytics applications and review input/output configuration\. It also grants permissions that allow a user to view a list of Kinesis streams and Kinesis Data Firehose delivery streams\. As the application is running, the user can view source data and real\-time analytics results in the console\.

   
+ **`AmazonKinesisAnalyticsFullAccess`** – Grants permissions for all Amazon Kinesis Data Analytics actions and all other permissions that allows a user to create and manage Amazon Kinesis Data Analytics applications\. However, note the following:

   
  + These permissions are not sufficient if the user wants to create a new IAM role in the console \(these permissions allow the user to select an existing role\)\. If you want the user to be able to create an IAM role in the console, add the `IAMFullAccess` AWS managed policy\.

     
  + A user must have permission for the `iam:PassRole` action to specify an IAM role when configuring Amazon Kinesis Data Analytics application\. This AWS managed policy grants permission for the `iam:PassRole` action to the user only on the IAM roles that start with the prefix `service-role/kinesis-analytics`\. 

    If the user wants to configure the Amazon Kinesis Data Analytics application with a role that does not have this prefix, you first must explicitly grant the user permission for the `iam:PassRole` action on the specific role\. 

**Note**  
You can review these permissions policies by signing in to the IAM console and searching for specific policies there\.

You can also create your own custom IAM policies to allow permissions for Amazon Kinesis Data Analytics actions and resources\. You can attach these custom policies to the IAM users or groups that require those permissions\. 

## Customer Managed Policy Examples<a name="access-policy-customer-managed-examples"></a>

The examples in this section provide a group of sample policies that you can attach to a user\. If you are new to creating policies, we recommend that you first create an IAM user in your account\. Then attach the policies to the user in sequence, as outlined in the steps in this section\. You can then use the console to verify the effects of each policy as you attach the policy to the user\. 

Initially, the user doesn't have permissions and can't do anything on the console\. As you attach policies to the user, you can verify that the user can perform various actions on the console\. 

We recommend that you use two browser windows\. In one window, create the user and grant permissions\. In the other, sign in to the AWS Management Console using the user's credentials and verify permissions as you grant them\.

 For examples that show how to create an IAM role that you can use as an execution role for your Amazon Kinesis Data Analytics application, see [Creating IAM Roles](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_create.html) in the *IAM User Guide*\.

**Topics**
+ [Step 1: Create an IAM User](#console-permissions-createuser)
+ [Step 2: Allow the User Permissions for Actions that Are Not Specific to Amazon Kinesis Data Analytics](#console-permissions-grant-non-ka-permissions)
+ [Step 3: Allow the User to View a List of Applications and View Details](#console-permissions-grant-list-applications)
+ [Step 4: Allow the User to Start a Specific Application](#console-permissions-start-app)
+ [Step 5: Allow the User to Create an Amazon Kinesis Data Analytics Application](#console-permissions-grant-create-applications)
+ [Step 6: Allow the Application to Use Lambda Preprocessing](#console-permissions-grant-lambda)

### Step 1: Create an IAM User<a name="console-permissions-createuser"></a>

First, you need to create an IAM user, add the user to an IAM group with administrative permissions, and then grant administrative permissions to the IAM user that you created\. You can then access AWS using a special URL and that IAM user's credentials\. 

For instructions, see [Creating Your First IAM User and Administrators Group](https://docs.aws.amazon.com/IAM/latest/UserGuide/getting-started_create-admin-group.html) in the *IAM User Guide*\. 

### Step 2: Allow the User Permissions for Actions that Are Not Specific to Amazon Kinesis Data Analytics<a name="console-permissions-grant-non-ka-permissions"></a>

First, grant a user permission for all actions that aren't specific to Amazon Kinesis Data Analytics that the user will need when working with Amazon Kinesis Data Analytics applications\. These include permissions for working with streams \(Amazon Kinesis Data Streams actions, Amazon Kinesis Data Firehose actions\), and permissions for CloudWatch actions\. Attach the following policy to the user\.

You need to update the policy by providing an IAM role name for which you want to grant the `iam:PassRole` permission, or specify a wildcard character \(\*\) indicating all IAM roles\. This is not a secure practice; however you might not have a specific IAM role created during this testing\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "kinesis:CreateStream",
                "kinesis:DeleteStream",
                "kinesis:DescribeStream",
                "kinesis:ListStreams",
                "kinesis:PutRecord",
                "kinesis:PutRecords"
            ],
            "Resource": "*"
        },
        {
            "Effect": "Allow",
            "Action": [
                "firehose:DescribeDeliveryStream",
                "firehose:ListDeliveryStreams"
            ],
            "Resource": "*"
        },
        {
            "Effect": "Allow",
            "Action": [
                "cloudwatch:GetMetricStatistics",
                "cloudwatch:ListMetrics"
            ],
            "Resource": "*"
        },
        {
            "Effect": "Allow",
            "Action": "logs:GetLogEvents",
            "Resource": "*"
        },
        {
            "Effect": "Allow",
            "Action": [
                "iam:ListPolicyVersions",
                "iam:ListRoles"
            ],
            "Resource": "*"
        },
        {
            "Effect": "Allow",
            "Action": "iam:PassRole",
            "Resource": "arn:aws:iam::*:role/service-role/role-name"
        }
    ]
}
```

### Step 3: Allow the User to View a List of Applications and View Details<a name="console-permissions-grant-list-applications"></a>

The following policy grants a user the following permissions:
+ Permission for the `kinesisanalytics:ListApplications` action so the user can view a list of applications\. This is a service\-level API call, and therefore you specify "\*" as the `Resource` value\.
+ Permission for the `kinesisanalytics:DescribeApplication` action so that you can get information about any of the applications\.

Add this policy to the user\. 

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "kinesisanalytics:ListApplications"
            ],
            "Resource": "*"
        },
        {
            "Effect": "Allow",
            "Action": [
                "kinesisanalytics:DescribeApplication"
            ],
            "Resource": "arn:aws:kinesisanalytics:aws-region:aws-account-id:application/*"
        }
    ]
}
```

 Verify these permissions by signing into the Amazon Kinesis Data Analytics console using the IAM user credentials\.

### Step 4: Allow the User to Start a Specific Application<a name="console-permissions-start-app"></a>

If you want the user to be able to start one of the existing Amazon Kinesis Data Analytics applications, attach the following policy to the user\. The policy provides the permission for the `kinesisanalytics:StartApplication` action\. You must update the policy by providing your account ID, AWS Region, and application name\. 

```
{
    "Version": "2012-10-17",
    "Statement": [
               {
            "Effect": "Allow",
            "Action": [
                "kinesisanalytics:StartApplication"                
            ],
            "Resource": "arn:aws:kinesisanalytics:aws-region:aws-account-id:application/application-name"
        }
    ]
}
```

### Step 5: Allow the User to Create an Amazon Kinesis Data Analytics Application<a name="console-permissions-grant-create-applications"></a>

If you want the user to create an Amazon Kinesis Data Analytics application, you can then attach the following policy to the user\. You must update the policy and provide an AWS Region, your account ID, and either a specific application name that you want the user to create, or a "\*" so that the user can specify any application name \(and thus create multiple applications\)\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "Stmt1473028104000",
            "Effect": "Allow",
            "Action": [
                "kinesisanalytics:CreateApplication"
            ],
            "Resource": [
                "*"
            ]
        },
     
        {
            "Effect": "Allow",
            "Action": [
                "kinesisanalytics:StartApplication",
                "kinesisanalytics:UpdateApplication",
                "kinesisanalytics:AddApplicationInput",
                "kinesisanalytics:AddApplicationOutput"
            ],
            "Resource": "arn:aws:kinesisanalytics:aws-region:aws-account-id:application/application-name"
        }
    ]
}
```

### Step 6: Allow the Application to Use Lambda Preprocessing<a name="console-permissions-grant-lambda"></a>

If you want the application to be able to use Lambda preprocessing, attach the following policy to the role\. For more information about Lambda preprocessing, see [Preprocessing Data Using a Lambda Function](lambda-preprocessing.md)\.

```
     {
       "Sid": "UseLambdaFunction",
       "Effect": "Allow",
       "Action": [
           "lambda:InvokeFunction",
           "lambda:GetFunctionConfiguration"
       ],
       "Resource": "<FunctionARN>"
   }
```