# Connect to Amazon Athena Using an Interface VPC Endpoint<a name="interface-vpc-endpoint"></a>

You can connect directly to Athena by using an [interface VPC endpoint \(AWS PrivateLink\)](https://docs.aws.amazon.com/vpc/latest/userguide/vpce-interface.html) and an [AWS Glue VPC endpoint](https://docs.aws.amazon.com/glue/latest/dg/vpc-endpoint.html) in your Virtual Private Cloud \(VPC\) instead of connecting over the internet\. When you use an interface VPC endpoint, communication between your VPC and Athena is conducted entirely within the AWS network\. Each VPC endpoint is represented by one or more [Elastic Network Interfaces](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-eni.html) \(ENIs\) with private IP addresses in your VPC subnets\.

The interface VPC endpoint connects your VPC directly to Athena without an internet gateway, NAT device, VPN connection, or AWS Direct Connect connection\. The instances in your VPC don't need public IP addresses to communicate with the Athena API\.

To use Athena through your VPC, you must connect from an instance that is inside the VPC or connect your private network to your VPC by using an Amazon Virtual Private Network \(VPN\) or AWS Direct Connect\. For information about Amazon VPN, see [VPN Connections](https://docs.aws.amazon.com/vpc/latest/userguide/vpn-connections.html) in the *Amazon Virtual Private Cloud User Guide*\. For information about AWS Direct Connect, see [Creating a Connection](https://docs.aws.amazon.com/directconnect/latest/UserGuide/create-connection.html) in the *AWS Direct Connect User Guide*\.

Athena supports VPC endpoints in all AWS Regions where both [Amazon VPC](https://docs.aws.amazon.com/general/latest/gr/rande.html#vpc_region) and [Athena](https://docs.aws.amazon.com/general/latest/gr/rande.html#athena) are available\.

You can create an interface VPC endpoint to connect to Athena using the AWS Management Console or AWS Command Line Interface \(AWS CLI\) commands\. For more information, see [Creating an Interface Endpoint](https://docs.aws.amazon.com/vpc/latest/userguide/vpce-interface.html#create-interface-endpoint)\.

After you create an interface VPC endpoint, if you enable [private DNS](https://docs.aws.amazon.com/vpc/latest/userguide/vpce-interface.html#vpce-private-dns) hostnames for the endpoint, the default Athena endpoint \(https://athena\.*Region*\.amazonaws\.com\) resolves to your VPC endpoint\.

If you do not enable private DNS hostnames, Amazon VPC provides a DNS endpoint name that you can use in the following format:

```
VPC_Endpoint_ID.athena.Region.vpce.amazonaws.com
```

For more information, see [Interface VPC Endpoints \(AWS PrivateLink\)](https://docs.aws.amazon.com/vpc/latest/userguide/vpce-interface.html) in the *Amazon VPC User Guide*\.

Athena supports making calls to all of its [API Actions](https://docs.aws.amazon.com/athena/latest/APIReference/API_Operations.html) inside your VPC\.

## Create a VPC Endpoint Policy for Athena<a name="api-private-link-policy"></a>

You can create a policy for Amazon VPC endpoints for Athena to specify the following:
+ The principal that can perform actions\.
+ The actions that can be performed\.
+ The resources on which actions can be performed\.

For more information, see [Controlling Access to Services with VPC Endpoints](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-endpoints-access.html) in the *Amazon VPC User Guide*\.

Whenever you use IAM policies, make sure that you follow IAM best practices\. For more information, see [Security best practices in IAM](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html) in the *IAM User Guide*\.

**Example – VPC Endpoint Policy for Athena Actions**  
The endpoint to which this policy is attached grants access to the listed `athena` actions to all principals in `workgroupA`\.  

```
{
  "Statement": [{
    "Principal": "*",
    "Effect": "Allow",
    "Action": [
      "athena:StartQueryExecution",
      "athena:RunQuery",
      "athena:GetQueryExecution",
      "athena:GetQueryResults",
      "athena:StopQueryExecution",
      "athena:ListWorkGroups",
      "athena:GetWorkGroup",
      "athena:TagResource"
    ],
    "Resource": [
      "arn:aws:athena:us-west-1:AWSAccountId:workgroup/workgroupA"
    ]
  }]
}
```

## <a name="notebook-private-link-vpn"></a>