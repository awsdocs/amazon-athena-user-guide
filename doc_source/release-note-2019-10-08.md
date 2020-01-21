# October 8, 2019<a name="release-note-2019-10-08"></a>

Published on 2019\-12\-17

[Amazon Athena](https://aws.amazon.com/athena/) now allows you to connect directly to Athena through an interface VPC endpoint in your Virtual Private Cloud \(VPC\)\. Using this feature, you can submit your queries to Athena securely without requiring an Internet Gateway in your VPC\.

To create an interface VPC endpoint to connect to Athena, you can use the AWS console or AWS Command Line Interface \(AWS CLI\)\. For information about creating an interface endpoint, see [Creating an Interface Endpoint](https://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/vpce-interface.html#create-interface-endpoint)\.

When you use an interface VPC endpoint, communication between your VPC and Athena APIs is secure and stays within the AWS network\. There are no additional Athena costs to use this feature\. Interface VPC endpoint [charges](https://aws.amazon.com/privatelink/pricing/) apply\.

To learn more about this feature, see [Connect to Amazon Athena Using an Interface VPC Endpoint](https://docs.aws.amazon.com/athena/latest/ug/interface-vpc-endpoint.html)\.