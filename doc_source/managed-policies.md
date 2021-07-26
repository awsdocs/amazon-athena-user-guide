# AWS managed policies for Amazon Athena<a name="managed-policies"></a>

To add permissions to users, groups, and roles, it is easier to use AWS managed policies than to write policies yourself\. It takes time and expertise to [create IAM customer managed policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_create-console.html) that provide your team with only the permissions they need\. To get started quickly, you can use our AWS managed policies\. These policies cover common use cases and are available in your AWS account\. For more information about AWS managed policies, see [AWS managed policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_managed-vs-inline.html#aws-managed-policies) in the *IAM User Guide*\.

AWS services maintain and update AWS managed policies\. You can't change the permissions in AWS managed policies\. Services occasionally add additional permissions to an AWS managed policy to support new features\. This type of update affects all identities \(users, groups, and roles\) where the policy is attached\. Services are most likely to update an AWS managed policy when a new feature is launched or when new operations become available\. Services do not remove permissions from an AWS managed policy, so policy updates won't break your existing permissions\.

Additionally, AWS supports managed policies for job functions that span multiple services\. For example, the **ReadOnlyAccess** AWS managed policy provides read\-only access to all AWS services and resources\. When a service launches a new feature, AWS adds read\-only permissions for new operations and resources\. For a list and descriptions of job function policies, see [AWS managed policies for job functions](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_job-functions.html) in the *IAM User Guide*\.

## Considerations when using managed policies with Athena<a name="managed-policies-considerations"></a>

Managed policies are easy to use and are updated automatically with the required actions as the service evolves\. When using managed policies with Athena, keep the following points in mind:
+ To allow or deny Amazon Athena service actions for yourself or other users using AWS Identity and Access Management \(IAM\), you attach identity\-based policies to principals, such as users or groups\. 
+ Each identity\-based policy consists of statements that define the actions that are allowed or denied\. For more information and step\-by\-step instructions for attaching a policy to a user, see [Attaching Managed Policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_managed-using.html#attach-managed-policy-console) in the *IAM User Guide*\. For a list of actions, see the [Amazon Athena API Reference](https://docs.aws.amazon.com/athena/latest/APIReference/)\.
+  *Customer\-managed* and *inline* identity\-based policies allow you to specify more detailed Athena actions within a policy to fine\-tune access\. We recommend that you use the `AmazonAthenaFullAccess` policy as a starting point and then allow or deny specific actions listed in the [Amazon Athena API Reference](https://docs.aws.amazon.com/athena/latest/APIReference/)\. For more information about inline policies, see [Managed Policies and Inline Policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_managed-vs-inline.html) in the *IAM User Guide*\.
+ If you also have principals that connect using JDBC, you must provide the JDBC driver credentials to your application\. For more information, see [Service Actions for JDBC Connections](policy-actions.md)\.
+ If you have encrypted the AWS Glue Data Catalog, you must specify additional actions in the identity\-based IAM policies for Athena\. For more information, see [Access to Encrypted Metadata in the AWS Glue Data Catalog](access-encrypted-data-glue-data-catalog.md)\.
+ If you create and use workgroups, make sure your policies include relevant access to workgroup actions\. For detailed information, see [ IAM Policies for Accessing Workgroups](workgroups-iam-policy.md) and [Workgroup Example Policies](example-policies-workgroup.md)\. 

## AWS managed policy: AmazonAthenaFullAccess<a name="amazonathenafullaccess-managed-policy"></a>

The `AmazonAthenaFullAccess` managed policy grants full access to Athena\. Attach it to IAM users and other principals who need full access to Athena\. You can attach the `AmazonAthenaFullAccess` policy to your IAM identities\.

### Permissions groupings<a name="amazonathenafullaccess-managed-policy-groupings"></a>

The `AmazonAthenaFullAccess` policy is grouped into the following sets of permissions\.
+ **`athena`** – Allows principals access to Athena resources\.
+ **`glue`** – Allows principals access to AWS Glue databases, tables, and partitions\. This is required so that the principal can use the AWS Glue Data Catalog with Athena\.
+ **`s3`** – Allows the principal to write and read query results from Amazon S3, to read publically available Athena data examples that reside in Amazon S3, and to list buckets\. This is required so that the principal can use Athena to work with Amazon S3\.
+ **`sns`** – Allows principals to list Amazon SNS topics and get topic attributes\. This enables principals to use Amazon SNS topics with Athena for monitoring and alert purposes\.
+ **`cloudwatch`** – Allows principals to create, read, and delete CloudWatch alarms\. For more information, see [Controlling Costs and Monitoring Queries with CloudWatch Metrics and Events](control-limits.md)\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "athena:*"
            ],
            "Resource": [
                "*"
            ]
        },
        {
            "Effect": "Allow",
            "Action": [
                "glue:CreateDatabase",
                "glue:DeleteDatabase",
                "glue:GetDatabase",
                "glue:GetDatabases",
                "glue:UpdateDatabase",
                "glue:CreateTable",
                "glue:DeleteTable",
                "glue:BatchDeleteTable",
                "glue:UpdateTable",
                "glue:GetTable",
                "glue:GetTables",
                "glue:BatchCreatePartition",
                "glue:CreatePartition",
                "glue:DeletePartition",
                "glue:BatchDeletePartition",
                "glue:UpdatePartition",
                "glue:GetPartition",
                "glue:GetPartitions",
                "glue:BatchGetPartition"
            ],
            "Resource": [
                "*"
            ]
        },
        {
            "Effect": "Allow",
            "Action": [
                "s3:GetBucketLocation",
                "s3:GetObject",
                "s3:ListBucket",
                "s3:ListBucketMultipartUploads",
                "s3:ListMultipartUploadParts",
                "s3:AbortMultipartUpload",
                "s3:CreateBucket",
                "s3:PutObject",
                "s3:PutBucketPublicAccessBlock"
            ],
            "Resource": [
                "arn:aws:s3:::aws-athena-query-results-*"
            ]
        },
        {
            "Effect": "Allow",
            "Action": [
                "s3:GetObject",
                "s3:ListBucket"
            ],
            "Resource": [
                "arn:aws:s3:::athena-examples*"
            ]
        },
        {
            "Effect": "Allow",
            "Action": [
                "s3:ListBucket",
                "s3:GetBucketLocation",
                "s3:ListAllMyBuckets"
            ],
            "Resource": [
                "*"
            ]
        },
        {
            "Effect": "Allow",
            "Action": [
                "sns:ListTopics",
                "sns:GetTopicAttributes"
            ],
            "Resource": [
                "*"
            ]
        },
        {
            "Effect": "Allow",
            "Action": [
                "cloudwatch:PutMetricAlarm",
                "cloudwatch:DescribeAlarms",
                "cloudwatch:DeleteAlarms"
            ],
            "Resource": [
                "*"
            ]
        }
    ]
}
```

## AWS managed policy: AWSQuicksightAthenaAccess<a name="awsquicksightathenaaccess-managed-policy"></a>

`AWSQuicksightAthenaAccess` grants access to actions that Amazon QuickSight requires for integration with Athena\. You can attach the `AWSQuicksightAthenaAccess` policy to your IAM identities\. Attach this policy only to principals who use Amazon QuickSight with Athena\. This policy includes some actions for Athena that are either deprecated and not included in the current public API, or that are used only with the JDBC and ODBC drivers\.

### Permissions groupings<a name="awsquicksightathenaaccess-managed-policy-groupings"></a>

The `AWSQuicksightAthenaAccess` policy is grouped into the following sets of permissions\.
+ **`athena`** – Allows the principal to run queries on Athena resources\.
+ **`glue`** – Allows principals access to AWS Glue databases, tables, and partitions\. This is required so that the principal can use the AWS Glue Data Catalog with Athena\.
+ **`s3`** – Allows the principal to write and read query results from Amazon S3\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "athena:BatchGetQueryExecution",
                "athena:CancelQueryExecution",
                "athena:GetCatalogs",
                "athena:GetExecutionEngine",
                "athena:GetExecutionEngines",
                "athena:GetNamespace",
                "athena:GetNamespaces",
                "athena:GetQueryExecution",
                "athena:GetQueryExecutions",
                "athena:GetQueryResults",
                "athena:GetQueryResultsStream",
                "athena:GetTable",
                "athena:GetTables",
                "athena:ListQueryExecutions",
                "athena:RunQuery",
                "athena:StartQueryExecution",
                "athena:StopQueryExecution"
            ],
            "Resource": [
                "*"
            ]
        },
        {
            "Effect": "Allow",
            "Action": [
                "glue:CreateDatabase",
                "glue:DeleteDatabase",
                "glue:GetDatabase",
                "glue:GetDatabases",
                "glue:UpdateDatabase",
                "glue:CreateTable",
                "glue:DeleteTable",
                "glue:BatchDeleteTable",
                "glue:UpdateTable",
                "glue:GetTable",
                "glue:GetTables",
                "glue:BatchCreatePartition",
                "glue:CreatePartition",
                "glue:DeletePartition",
                "glue:BatchDeletePartition",
                "glue:UpdatePartition",
                "glue:GetPartition",
                "glue:GetPartitions",
                "glue:BatchGetPartition"
            ],
            "Resource": [
                "*"
            ]
        },
        {
            "Effect": "Allow",
            "Action": [
                "s3:GetBucketLocation",
                "s3:GetObject",
                "s3:ListBucket",
                "s3:ListBucketMultipartUploads",
                "s3:ListMultipartUploadParts",
                "s3:AbortMultipartUpload",
                "s3:CreateBucket",
                "s3:PutObject",
                "s3:PutBucketPublicAccessBlock"
            ],
            "Resource": [
                "arn:aws:s3:::aws-athena-query-results-*"
            ]
        }
    ]
}
```

## Athena updates to AWS managed policies<a name="managed-policies-updates"></a>

View details about updates to AWS managed policies for Athena since this service began tracking these changes\.


| Change | Description | Date | 
| --- | --- | --- | 
|  [AmazonAthenaFullAccess](#amazonathenafullaccess-managed-policy) and [AWSQuicksightAthenaAccess](#awsquicksightathenaaccess-managed-policy) – Updates to existing policies  |  Athena added `s3:PutBucketPublicAccessBlock` to enable the blocking of public access on the buckets created by Athena\.  | July 7, 2021 | 
|  Athena started tracking changes  |  Athena started tracking changes for its AWS managed policies\.  | July 7, 2021 | 