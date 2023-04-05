# Connect to Amazon Athena using an interface VPC endpoint<a name="interface-vpc-endpoint"></a>

You can improve the security posture of your VPC by using an [interface VPC endpoint \(AWS PrivateLink\)](https://docs.aws.amazon.com/vpc/latest/userguide/vpce-interface.html) and an [AWS Glue VPC endpoint](https://docs.aws.amazon.com/glue/latest/dg/vpc-endpoint.html) in your Virtual Private Cloud \(VPC\)\. An interface VPC endpoint improves security by giving you control over what destinations can be reached from inside your VPC\. Each VPC endpoint is represented by one or more [Elastic network interfaces](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-eni.html) \(ENIs\) with private IP addresses in your VPC subnets\.

The interface VPC endpoint connects your VPC directly to Athena without an internet gateway, NAT device, VPN connection, or AWS Direct Connect connection\. The instances in your VPC don't need public IP addresses to communicate with the Athena API\.

To use Athena through your VPC, you must connect from an instance that is inside the VPC or connect your private network to your VPC by using an Amazon Virtual Private Network \(VPN\) or AWS Direct Connect\. For information about Amazon VPN, see [VPN connections](https://docs.aws.amazon.com/vpc/latest/userguide/vpn-connections.html) in the *Amazon Virtual Private Cloud User Guide*\. For information about AWS Direct Connect, see [Creating a connection](https://docs.aws.amazon.com/directconnect/latest/UserGuide/create-connection.html) in the *AWS Direct Connect User Guide*\.

Athena supports VPC endpoints in all AWS Regions where both [Amazon VPC](https://docs.aws.amazon.com/general/latest/gr/rande.html#vpc_region) and [Athena](https://docs.aws.amazon.com/general/latest/gr/rande.html#athena) are available\.

You can create an interface VPC endpoint to connect to Athena using the AWS Management Console or AWS Command Line Interface \(AWS CLI\) commands\. For more information, see [Creating an interface endpoint](https://docs.aws.amazon.com/vpc/latest/userguide/vpce-interface.html#create-interface-endpoint)\.

After you create an interface VPC endpoint, if you enable [private DNS](https://docs.aws.amazon.com/vpc/latest/userguide/vpce-interface.html#vpce-private-dns) hostnames for the endpoint, the default Athena endpoint \(https://athena\.*Region*\.amazonaws\.com\) resolves to your VPC endpoint\.

If you do not enable private DNS hostnames, Amazon VPC provides a DNS endpoint name that you can use in the following format:

```
VPC_Endpoint_ID.athena.Region.vpce.amazonaws.com
```

For more information, see [Interface VPC endpoints \(AWS PrivateLink\)](https://docs.aws.amazon.com/vpc/latest/userguide/vpce-interface.html) in the *Amazon VPC User Guide*\.

Athena supports making calls to all of its [API actions](https://docs.aws.amazon.com/athena/latest/APIReference/API_Operations.html) inside your VPC\.

## Create a VPC endpoint policy for Athena<a name="api-private-link-policy"></a>

You can create a policy for Amazon VPC endpoints for Athena to specify restrictions like the following:
+ **Principal** – The principal that can perform actions\.
+ **Actions** – The actions that can be performed\.
+ **Resources** – The resources on which actions can be performed\.
+ **Only trusted identities** – Use the `aws:PrincipalOrgId` condition to restrict access to only credentials that are part of your AWS organization\. This can help prevent access by unintended principals\. 
+ **Only trusted resources** – Use the `aws:ResourceOrgId` condition to prevent access to unintended resources\. 
+ **Only trusted identities and resources** – Create a combined policy for a VPC endpoint that helps prevent access to unintended principals and resources\. 

For more information, see [Controlling access to services with VPC endpoints](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-endpoints-access.html) in the *Amazon VPC User Guide* and [Appendix 2 – VPC endpoint policy examples](https://docs.aws.amazon.com/whitepapers/latest/building-a-data-perimeter-on-aws/appendix-2-vpc-endpoint-policy-examples.html) in the AWS Whitepaper *Building a data perimeter on AWS*\.

**Example – VPC endpoint policy for Athena actions**  
The following example allows requests by organization identities to organization resources, allows requests by AWS service principals, and grants access to the listed `athena` actions to all principals in `workgroupA`\.  

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "AllowRequestsByOrgsIdentitiesToOrgsResources",
            "Effect": "Allow",
            "Principal": {
                "AWS": "*"
            },
            "Action": "*",
            "Resource": "*",
            "Condition": {
                "StringEquals": {
                    "aws:PrincipalOrgID": "my-org-id",
                    "aws:ResourceOrgID": "my-org-id"
                }
            }
        },
        {
            "Sid": "AllowRequestsByAWSServicePrincipals",
            "Effect": "Allow",
            "Principal": {
                "AWS": "*"
            },
            "Action": "*",
            "Resource": "*",
            "Condition": {
                "Bool": {
                    "aws:PrincipalIsAWSService": "true"
                }
            }
        },
        {
            "Sid": "PermitAccessToSpecificWorkgroup",
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
                "arn:${AWS::Partition}:athena:${AWS::Region}:AWSAccountId:workgroup/workgroupA"
            ]
        }
    ]
}
```

Whenever you use IAM policies, make sure that you follow IAM best practices\. For more information, see [Security best practices in IAM](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html) in the *IAM User Guide*\.

## <a name="notebook-private-link-vpn"></a>