# VPC Application Permissions<a name="vpc-permissions"></a>

This section describes the permission policies your application will need to work with your VPC\. For more information about using permissions policies, see [Identity and Access Management for Amazon Kinesis Data Analytics](security-iam.md)\.

The following permissions policy grants your application the necessary permissions to interact with a VPC\. To use this permission policy, add it to your application's execution role\.

## Permissions Policy for Accessing an Amazon VPC<a name="vpc-permissions-policy"></a>

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "VPCReadOnlyPermissions",
      "Effect": "Allow",
      "Action": [
        "ec2:DescribeVpcs",
        "ec2:DescribeSubnets",
        "ec2:DescribeSecurityGroups",
        "ec2:DescribeDhcpOptions"
      ],
      "Resource": "*"
    },
    {
      "Sid": "ENIReadWritePermissions",
      "Effect": "Allow",
      "Action": [
        "ec2:CreateNetworkInterface",
        "ec2:CreateNetworkInterfacePermission",
        "ec2:DescribeNetworkInterfaces",
        "ec2:DeleteNetworkInterface"
      ],
      "Resource": "*"
    }

    ]
}
```

**Note**  
When you specify application resources using the console \(such as CloudWatch Logs or an Amazon VPC\), the console modifies your application execution role to grant permission to access those resources\. You only need to manually modify your application's execution role if you create your application without using the console\.