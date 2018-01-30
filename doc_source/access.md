# Setting User and Amazon S3 Bucket Permissions<a name="access"></a>

To run queries in Athena, you must have the appropriate permissions for:

+ The Athena actions\.

+ The Amazon S3 locations where the underlying data is stored that you are going to query in Athena\.

If you are an administrator for other users, make sure that they have appropriate permissions associated with their user profiles\.

## IAM Policies for User Access<a name="managed-policies"></a>

To allow or deny Athena service actions for yourself or other users, use IAM policies attached to principals, such as users or groups\.

Each IAM policy consists of statements that define the actions that are allowed or denied\. For a list of actions, see the [Amazon Athena API Reference](http://docs.aws.amazon.com/athena/latest/APIReference/)\.

 *Managed policies* are easy to use and are automatically updated with the required actions as the service evolves\.

The `AmazonAthenaFullAccess` policy is the managed policy for Athena\. Attach this policy to users and other principals who need full access to Athena\. For more information and step\-by\-step instructions for attaching a policy to a user, see [Attaching Managed Policies](http://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_managed-using.html#attach-managed-policy-console) in the *AWS Identity and Access Management User Guide*\.

 *Customer\-managed* and *inline* policies allow you to specify more granular Athena actions within a policy to fine\-tune access\. We recommend that you use the `AmazonAthenaFullAccess` policy as a starting point and then allow or deny specific actions listed in the [Amazon Athena API Reference](http://docs.aws.amazon.com/athena/latest/APIReference/)\. For more information about inline policies, see [Managed Policies and Inline Policies](http://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_managed-vs-inline.html) in the *AWS Identity and Access Management User Guide*\.

If you also have principals that connect using JDBC, you must allow additional actions not listed in the API\. For more information, see Service Actions for JDBC Connections\.

## AmazonAthenaFullAccess Managed Policy<a name="amazonathenafullaccess-managed-policy"></a>

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
                "s3:GetObject"
            ],
            "Resource": [
                "arn:aws:s3:::athena-examples*"
            ]
        }
    ]
}
```

## AWSQuicksightAthenaAccess Managed Policy<a name="awsquicksightathenaaccess-managed-policy"></a>

An additional managed policy, `AWSQuicksightAthenaAccess`, grants access to actions that Amazon QuickSight needs to integrate with Athena\. This policy includes deprecated actions for Athena that are not in the API\. Attach this policy only to principals who use Amazon QuickSight in conjunction with Athena\.

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

## Access through JDBC Connections<a name="policy-actions"></a>

To gain access to AWS services and resources, such as Athena and the Amazon S3 buckets, provide JDBC driver credentials to your application\. See Connect with the JDBC Driver\.

## Amazon S3 Permissions<a name="s3-permissions"></a>

In addition to the allowed actions for Athena that you define in policies, if you or your users need to create tables and work with underlying data, you must grant appropriate access to the Amazon S3 location of the data\.

You can do this using user policies, bucket policies, or both\. For detailed information and scenarios about how to grant Amazon S3 access, see [Example Walkthroughs: Managing Access](http://docs.aws.amazon.com/AmazonS3/latest/dev/example-walkthroughs-managing-access.html) in the *Amazon Simple Storage Service Developer Guide*\. For more information and an example of which Amazon S3 actions to allow, see the example bucket policy later in this topic\.

**Note**  
Athena does not support restricting or allowing access to Amazon S3 resources based on the `aws:SourceIp` condition key\.

## Cross\-account Permissions<a name="cross-account-permissions"></a>

A common scenario is granting access to users in an account different from the bucket owner so that they can perform queries\. In this case, use a bucket policy to grant access\.

The following example bucket policy, created and applied to bucket `s3://my-athena-data-bucket` by the bucket owner, grants access to all users in account `123456789123`, which is a different account\.

```
{
   "Version": "2012-10-17",
   "Id": "MyPolicyID",
   "Statement": [
      {
          "Sid": "MyStatementSid",
          "Effect": "Allow",
          "Principal": {
             "AWS": "arn:aws:iam::123456789123:root"
          },
          "Action": [
             "s3:GetBucketLocation",
             "s3:GetObject",
             "s3:ListBucket",
             "s3:ListBucketMultipartUploads",
             "s3:ListMultipartUploadParts",
             "s3:AbortMultipartUpload",
             "s3:PutObject"
          ],
          "Resource": [
             "arn:aws:s3:::my-athena-data-bucket",
             "arn:aws:s3:::my-athena-data-bucket/*"
          ]
       }
    ]
 }
```

To grant access to a particular user in an account, replace the `Principal` key with a key that specifies the user instead of `root`\. For example, for user profile `Dave`, use `arn:aws:iam::123456789123:user/Dave`\.