# Setting User and Amazon S3 Bucket Permissions<a name="access"></a>

To run queries in Athena, you must have the appropriate permissions for:
+ The Athena actions\.
+ The Amazon S3 locations where the underlying data is stored that you are going to query in Athena\.
+ The encrypted metadata in the AWS Glue Data Catalog \(if you migrated to using it in Athena and the metadata is encrypted\)\.

If you are an administrator for other users, make sure that they have appropriate permissions associated with their user profiles\.

**Topics**
+ [IAM Policies for User Access](#managed-policies)
+ [AmazonAthenaFullAccess Managed Policy](#amazonathenafullaccess-managed-policy)
+ [AWSQuicksightAthenaAccess Managed Policy](#awsquicksightathenaaccess-managed-policy)
+ [Access through JDBC and ODBC Connections](#policy-actions)
+ [Access to Amazon S3](#s3-permissions)
+ [Access to Encrypted Metadata in the AWS Glue Data Catalog](#access-encrypted-data-glue-data-catalog)
+ [Cross\-account Access](#cross-account-permissions)

## IAM Policies for User Access<a name="managed-policies"></a>

To allow or deny Athena service actions for yourself or other users, use IAM policies attached to principals, such as users or groups\.

Each IAM policy consists of statements that define the actions that are allowed or denied\. For a list of actions, see the [Amazon Athena API Reference](http://docs.aws.amazon.com/athena/latest/APIReference/)\.

 *Managed policies* are easy to use and are automatically updated with the required actions as the service evolves\. For more information and step\-by\-step instructions for attaching a policy to a user, see [Attaching Managed Policies](http://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_managed-using.html#attach-managed-policy-console) in the *AWS Identity and Access Management User Guide*\.

Athena has these managed policies:
+ The `AmazonAthenaFullAccess` managed policy grants full access to Athena\. Attach it to users and other principals who need full access to Athena\. See [AmazonAthenaFullAccess Managed Policy](#amazonathenafullaccess-managed-policy)\.
+ The `AWSQuicksightAthenaAccess` managed policy grants access to actions that Amazon QuickSightneeds to integrate with Athena\. Attach this policy to principals who use Amazon QuickSight in conjunction with Athena\. See [AWSQuicksightAthenaAccess Managed Policy](#awsquicksightathenaaccess-managed-policy)\.

 *Customer\-managed* and *inline* policies allow you to specify more granular Athena actions within a policy to fine\-tune access\. We recommend that you use the `AmazonAthenaFullAccess` policy as a starting point and then allow or deny specific actions listed in the [Amazon Athena API Reference](http://docs.aws.amazon.com/athena/latest/APIReference/)\. For more information about inline policies, see [Managed Policies and Inline Policies](http://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_managed-vs-inline.html) in the *AWS Identity and Access Management User Guide*\.

If you also have principals that connect using JDBC, you must provide the JDBC driver credentials to your application\. For more information, see [Service Actions for JDBC Connections](#policy-actions)\.

If you have migrated to using AWS Glue with Athena, and choose to encrypt your AWS Glue Data Catalog, you must add additional actions to the policies you use in Athena\. For more information, see [Access to Encrypted Metadata in the AWS Glue Data Catalog](#access-encrypted-data-glue-data-catalog)\.

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

To gain access to AWS services and resources, such as Athena and the Amazon S3 buckets, provide the JDBC and ODBC driver credentials to your application\. If you are using the JDBC driver version 2\.0\.2 or higher, or the ODBC driver version 1\.0\.2 or higher, ensure the access policy includes all of the actions listed in [AWSQuicksightAthenaAccess Managed Policy](#awsquicksightathenaaccess-managed-policy)\.

For information about the latest version of the JDBC driver, see [Connect with the JDBC Driver](connect-with-jdbc.md)\.

For information about the latest version of the ODBC driver, see [Connect with the ODBC Driver](connect-with-odbc.md)\.

## Access to Amazon S3<a name="s3-permissions"></a>

In addition to the allowed actions for Athena that you define in policies, if you or your users need to create tables and work with underlying data, you must grant appropriate access to the Amazon S3 location of the data\.

You can do this using user policies, bucket policies, or both\. For detailed information and scenarios about how to grant Amazon S3 access, see [Example Walkthroughs: Managing Access](http://docs.aws.amazon.com/AmazonS3/latest/dev/example-walkthroughs-managing-access.html) in the *Amazon Simple Storage Service Developer Guide*\. For more information and an example of which Amazon S3 actions to allow, see the example bucket policy later in this topic\.

**Note**  
Athena does not support restricting or allowing access to Amazon S3 resources based on the `aws:SourceIp` condition key\.

## Access to Encrypted Metadata in the AWS Glue Data Catalog<a name="access-encrypted-data-glue-data-catalog"></a>

The encryption of objects in the AWS Glue Data Catalog is only available if you have migrated to using AWS Glue with Athena\. 

You can optionally enable encryption in the AWS Glue Data Catalog using the AWS Glue console, or the API\. For information, see [Encrypting Your Data Catalog](http://docs.aws.amazon.com/glue/latest/dg/encrypt-glue-data-catalog.html) in the *AWS Glue Developer Guide*\.

 If you choose to encrypt your AWS Glue Data Catalog, you must add the following actions to all of your policies used to access Athena:

```
{
 "Version": "2012-10-17",
 "Statement": {
 "Effect": "Allow",
     "Action": [
           "kms:GenerateDataKey",
           "kms:Decrypt",  
           "kms:Encrypt"
      ],
     "Resource": "(arn of key being used to encrypt the catalog)"
   }
}
```

## Cross\-account Access<a name="cross-account-permissions"></a>

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