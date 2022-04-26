# Creating a VPC for a data source connector<a name="athena-connectors-vpc-creation"></a>

Some Athena data source connectors require a VPC and a security group\. This topic shows you how to create a VPC with a subnet and a security group for the VPC\. As part of this process, you retrieve the IDs for the VPC, subnet, and security group that you create\. These IDs are required when you configure your connector for use with Athena\.

**To create a VPC for an Athena data source connector**

1. Sign in to the AWS Management Console and open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, ensure that **New VPC Experience** is selected\.

1. Choose **Launch VPC Wizard**\.

1. Under **VPC Settings**, for **Resources to create**, choose **VPC, subnets, etc\.**

1. For **Auto\-generate**, enter a value that will be used to generate name tags for all resources in your VPC\.

1. Choose **Create VPC**\.

1. Choose **View VPC**\.

1. In the **Details** section, for **VPC ID**, copy your VPC ID for later reference\.

Now you are ready to retrieve the subnet ID for the VPC that you just created\.

**To retrieve your VPC subnet ID**

1. In the VPC console navigation pane, choose **Subnets**\.

1. Choose the name corresponding to the subnet that you created\.

1. In the **Details** section, for **Subnet ID**, copy your subnet ID for later reference\.

Next, you create a security group for your VPC\.

**To create a security group for your VPC**

1. In the VPC console navigation pane, choose **Security**, **Security Groups**\.

1. Choose **Create security group**\.

1. On the **Create security group** page, enter the following information:
   + For **Security group name**, enter a name for your security group\.
   + For **Description**, enter a description for the security group\. This field is required\.
   + For **VPC**, enter the VPC ID of the VPC that you created for your data source connector\.
   + For **Inbound rules** and **Outbound rules**, add any inbound and outbound rules that you require\.

1. Choose **Create security group**\.

1. On the **Details** page for the security group, copy the **Security group ID** for later reference\.