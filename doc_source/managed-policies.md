# Managed Policies for User Access<a name="managed-policies"></a>

To allow or deny Athena service actions for yourself or other users, use identity\-based \(IAM\) policies attached to principals, such as users or groups\.

Each identity\-based \(IAM \) policy consists of statements that define the actions that are allowed or denied\. For a list of actions, see the [Amazon Athena API Reference](https://docs.aws.amazon.com/athena/latest/APIReference/)\.

 *Managed policies* are easy to use and are automatically updated with the required actions as the service evolves\. For more information and step\-by\-step instructions for attaching a policy to a user, see [Attaching Managed Policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_managed-using.html#attach-managed-policy-console) in the *AWS Identity and Access Management User Guide*\.

Athena has these managed policies:
+ The `AmazonAthenaFullAccess` managed policy grants full access to Athena\. Attach it to users and other principals who need full access to Athena\. See [AmazonAthenaFullAccess Managed Policy](#amazonathenafullaccess-managed-policy)\.
+ The `AWSQuicksightAthenaAccess` managed policy grants access to actions that Amazon QuickSight needs to integrate with Athena\. Attach this policy to principals who use Amazon QuickSight in conjunction with Athena\. See [AWSQuicksightAthenaAccess Managed Policy](#awsquicksightathenaaccess-managed-policy)\.

 *Customer\-managed* and *inline* identity\-based policies allow you to specify more detailed Athena actions within a policy to fine\-tune access\. We recommend that you use the `AmazonAthenaFullAccess` policy as a starting point and then allow or deny specific actions listed in the [Amazon Athena API Reference](https://docs.aws.amazon.com/athena/latest/APIReference/)\. For more information about inline policies, see [Managed Policies and Inline Policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_managed-vs-inline.html) in the *AWS Identity and Access Management User Guide*\.

If you also have principals that connect using JDBC, you must provide the JDBC driver credentials to your application\. For more information, see [Service Actions for JDBC Connections](#policy-actions)\.

If you have migrated to using AWS Glue with Athena, and have chosen to encrypt your AWS Glue Data Catalog, you must specify additional actions to the identity\-based IAM policies that you use in Athena\. For more information, see [Access to Encrypted Metadata in the AWS Glue Data Catalog](access-encrypted-data-glue-data-catalog.md)\.

**Important**  
If you create and use workgroups, make sure your policies include appropriate access to workgroup actions\. For detailed information, see [ IAM Policies for Accessing Workgroups](workgroups-iam-policy.md) and [Workgroup Example Policies](example-policies-workgroup.md)\. 

## AmazonAthenaFullAccess Managed Policy<a name="amazonathenafullaccess-managed-policy"></a>

The `AmazonAthenaFullAccess` managed policy grants full access to Athena\. 

Managed policy contents change, so the policy shown here may be out\-of\-date\. Check the IAM console for the most up\-to\-date policy\.

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
                "s3:PutObject"
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

## AWSQuicksightAthenaAccess Managed Policy<a name="awsquicksightathenaaccess-managed-policy"></a>

An additional managed policy, `AWSQuicksightAthenaAccess`, grants access to actions that Amazon QuickSight needs to integrate with Athena\. This policy includes some actions for Athena that are either deprecated and not included in the current public API, or that are used only with the JDBC and ODBC drivers\. Attach this policy only to principals who use Amazon QuickSight in conjunction with Athena\.

Managed policy contents change, so the policy shown here may be out\-of\-date\. Check the IAM console for the most up\-to\-date policy\.

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
                "s3:PutObject"
            ],
            "Resource": [
                "arn:aws:s3:::aws-athena-query-results-*"
            ]
        }
    ]
}
```

## Access through JDBC and ODBC Connections<a name="policy-actions"></a>

To gain access to AWS services and resources, such as Athena and the Amazon S3 buckets, provide the JDBC or ODBC driver credentials to your application\. If you are using the JDBC driver version 2\.0\.2, or the ODBC driver version 1\.0\.3, ensure the access policy includes all of the actions listed in [AWSQuicksightAthenaAccess Managed Policy](#awsquicksightathenaaccess-managed-policy)\.

For information about the latest version of the JDBC driver, see [Connect with the JDBC Driver](connect-with-jdbc.md)\.

For information about the latest version of the ODBC driver, see [Connect with the ODBC Driver](connect-with-odbc.md)\.
