# Amazon Kinesis Data Analytics API Permissions: Actions, Permissions, and Resources Reference<a name="api-permissions-reference"></a>

When you are setting up [Access Control](authentication-and-access-control.md#access-control) and writing a permissions policy that you can attach to an IAM identity \(identity\-based policies\), you can use the following table as a reference\. The table lists each Amazon Kinesis Data Analytics API operation, the corresponding actions for which you can grant permissions to perform the action, and the AWS resource for which you can grant the permissions\. You specify the actions in the policy's `Action` field, and you specify the resource value in the policy's `Resource` field\. 

You can use AWS\-wide condition keys in your Amazon Kinesis Data Analytics policies to express conditions\. For a complete list of AWS\-wide keys, see [Available Keys](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements.html#AvailableKeys) in the *IAM User Guide*\. 

**Note**  
To specify an action, use the `kinesisanalytics` prefix followed by the API operation name \(for example, `kinesisanalytics:AddApplicationInput`\)\.

If you see an expand arrow \(**↗**\) in the upper\-right corner of the table, you can open the table in a new window\. To close the window, choose the close button \(**X**\) in the lower\-right corner\.


**Amazon Kinesis Data Analytics API and Required Permissions for Actions**  

| Amazon Kinesis Data Analytics API Operations | Required Permissions \(API Actions\) | Resources | 
| --- | --- | --- | 
|  [AddApplicationInput](API_AddApplicationInput.md)   |  kinesisanalytics:AddApplicationInput  |  `arn:aws:kinesisanalytics: region:accountId:application/application-name`  | 
|  [AddApplicationOutput](API_AddApplicationOutput.md)   |  kinesisanalytics:AddApplicationOutput  |  `arn:aws:kinesisanalytics: region:accountId:application/application-name`  | 
|  [AddApplicationReferenceDataSource](API_AddApplicationReferenceDataSource.md)   |  kinesisanalytics:AddApplicationReferenceDataSource  |  `arn:aws:kinesisanalytics: region:accountId:application/application-name`  | 
|  [CreateApplication](API_CreateApplication.md)   |  kinesisanalytics:CreateApplication  |  `arn:aws:kinesisanalytics: region:accountId:application/application-name`  | 
|  [DeleteApplication](API_DeleteApplication.md)   |  kinesisanalytics:DeleteApplication  |  `arn:aws:kinesisanalytics: region:accountId:application/application-name`  | 
|  [DeleteApplicationOutput](API_DeleteApplicationOutput.md)   |  kinesisanalytics:DeleteApplicationOutput  |  `arn:aws:kinesisanalytics: region:accountId:application/application-name`  | 
|  [DeleteApplicationReferenceDataSource](API_DeleteApplicationReferenceDataSource.md)   |  kinesisanalytics:DeleteApplicationReferenceDataSource  |  `arn:aws:kinesisanalytics: region:accountId:application/application-name`  | 
|  [DescribeApplication](API_DescribeApplication.md)   |  kinesisanalytics:DescribeApplication  |  `arn:aws:kinesisanalytics: region:accountId:application/application-name`  | 
|  [DiscoverInputSchema](API_DiscoverInputSchema.md)   |  kinesisanalytics:DiscoverInputSchema  |  \*  | 
|  [ListApplications](API_ListApplications.md)   |  kinesisanalytics:ListApplications  |  \*  | 
|  [StartApplication](API_StartApplication.md)   |  kinesisanalytics:StartApplication  |  `arn:aws:kinesisanalytics: region:accountId:application/application-name`  | 
|  [StopApplication](API_StopApplication.md)   |  kinesisanalytics:StopApplication  |  `arn:aws:kinesisanalytics: region:accountId:application/application-name`  | 
|  [UpdateApplication](API_UpdateApplication.md)   |  kinesisanalytics:UpdateApplication  |  `arn:aws:kinesisanalytics: region:accountId:application/application-name`  | 
|  Access or sample data in the console   |  kinesisanalytics:[GetApplicationState](#api-permissions-reference-gas)  |  `arn:aws:kinesisanalytics: region:accountId:application/application-name`  | 

## GetApplicationState<a name="api-permissions-reference-gas"></a>

The console uses an internal method called `GetApplicationState` to sample or access application data\. Your Kinesis Data Analytics service application needs to have permissions for the internal `kinesisanalytics:GetApplicationState` API to sample or access application data through the AWS Management Console\.