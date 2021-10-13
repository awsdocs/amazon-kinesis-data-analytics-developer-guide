# Internet and Service Access for a VPC\-Connected Kinesis Data Analytics application<a name="vpc-internet"></a>

By default, when you connect a Kinesis Data Analytics application to a VPC in your account, it does not have access to the internet unless the VPC provides access\. If the application needs internet access, the following need to be true:
+ The Kinesis Data Analytics application should only be configured with private subnets\.
+ The VPC must contain a NAT gateway or instance in a public subnet\.
+ A route must exist for outbound traffic from the private subnets to the NAT gateway in a public subnet\.

**Note**  
Several services offer [VPC endpoints](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-endpoints.html)\. You can use VPC endpoints to connect to Amazon services from within a VPC without internet access\. 

Whether a subnet is public or private depends on its route table\. Every route table has a default route, which determines the next hop for packets that have a public destination\.
+ **For a Private subnet:** The default route points to a NAT gateway \(nat\-\.\.\.\) or NAT instance \(eni\-\.\.\.\)\.
+ **For a Public subnet:** The default route points to an internet gateway \(igw\-\.\.\.\)\.

Once you configure your VPC with a public subnet \(with a NAT\) and one or more private subnets, do the following to identify your private and public subnets:
+ In the VPC console, from the navigation pane, choose **Subnets**\.
+ Select a subnet, and then choose the **Route Table** tab\. Verify the default route:
  + **Public subnet:** Destination: 0\.0\.0\.0/0, Target: igw\-…
  + **Private subnet:** Destination: 0\.0\.0\.0/0, Target: nat\-… or eni\-…

To associate the Kinesis Data Analytics application with private subnets:
+ Open the Kinesis Data Analytics console at [ https://console\.aws\.amazon\.com/kinesisanalytics](https://console.aws.amazon.com/kinesisanalytics)\.
+ On the **Kinesis Analytics applications** page, choose your application, and choose **Application details**\.
+ On the page for your application, choose **Configure**\.
+ In the **VPC Connectivity** section, choose the VPC to associate with your application\. Choose the subnets and security group associated with your VPC that you want the application to use to access VPC resources\.
+ Choose **Update**\.

## Related Information<a name="vpc-internet-related"></a>

[Creating a VPC with Public and Private Subnets](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Scenario2.html)

[NAT gateway basics](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-nat-gateway.html#nat-gateway-basics)