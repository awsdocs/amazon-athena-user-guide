# Allow Lambda Function Access to External Hive Metastores<a name="hive-metastore-iam-access-lambda"></a>

To invoke a Lambda function in your account, you must create a role that has the following permissions:
+ `AWSLambdaVPCAccessExecutionRole` – An [AWS Lambda Execution Role](https://docs.aws.amazon.com/lambda/latest/dg/lambda-intro-execution-role.html) permission to manage elastic network interfaces that connect your function to a VPC\. Ensure that you have a sufficient number of network interfaces and IP addresses available\.
+ `AmazonAthenaFullAccess` – The [AmazonAthenaFullAccess](amazonathenafullaccess-managed-policy.md) managed policy grants full access to Athena\.
+ An Amazon S3 policy to allow the Lambda function to write to S3 and to allow Athena to read from S3\.

For example, the following policy defines the permission for the spill location `s3:\\mybucket\spill`\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "s3:GetBucketLocation",
                "s3:GetObject",
                "s3:ListBucket",
                "s3:PutObject"
            ],
            "Resource": [
                "arn:aws:s3:::mybucket/spill"
            ]
        }
    ]
}
```

## Creating Lambda Functions<a name="hive-metastore-iam-access-lambda-creating-lambda-functions"></a>

To create a Lambda function in your account, function development permissions or the `AWSLambdaFullAccess` role are required\. For more information, see [Identity\-based IAM Policies for AWS Lambda](https://docs.aws.amazon.com/lambda/latest/dg/access-control-identity-based.html)\.

Because Athena uses the AWS Serverless Application Repository to create Lambda functions, the superuser or administrator who creates Lambda functions should also have IAM policies [to allow Athena federated queries](federated-query-iam-access.md)\.

## Catalog Registration and Metadata API Operations<a name="hive-metastore-iam-access-lambda-catalog-registration-and-metadata-api-operations"></a>

For access to catalog registration API and metadata API operations, use the [AmazonAthenaFullAccess managed policy](amazonathenafullaccess-managed-policy.md)\. If you do not use this policy, add the following API operations to your Athena policies:

```
        {
            "Effect": "Allow",
            "Action": [
                "athena:ListDataCatalogs",
                "athena:GetDataCatalog",
                "athena:CreateDataCatalog",
                "athena:UpdateDataCatalog",
                "athena:DeleteDataCatalog",
                "athena:GetDatabase",
                "athena:ListDatabases",
                "athena:GetTableMetadata",
                "athena:ListTableMetadata"
            ],
            "Resource": [
                "*"
            ]
        }
```

## Cross Region Lambda Invocation<a name="hive-metastore-iam-access-lambda-cross-region-invocation"></a>

To invoke a Lambda function in a region other than the region in which you are running Athena queries, use the full ARN of the Lambda function\. By default, Athena invokes Lambda functions defined in the same region\. If you need to invoke a Lambda function to access a Hive metastore in a region other than the region in which you run Athena queries, you must provide the full ARN of the Lambda function\.

For example, suppose you define the catalog `ehms` on the Europe \(Frankfurt\) Region `eu-central-1` to use the following Lambda function in the US East \(N\. Virginia\) Region\.

```
arn:aws:lambda:us-east-1:111122223333:function:external-hms-service-new     
```

When you specify the full ARN in this way, Athena can call the `external-hms-service-new` Lambda function on `us-east-1` to fetch the Hive metastore data from `eu-central-1`\.

**Note**  
The catalog `ehms` should be registered in the same region that you run Athena queries\.

## Cross Account Lambda Invocation<a name="hive-metastore-iam-access-lambda-cross-account-invocation"></a>

Sometimes you might require access to a Hive metastore from a different account\. For example, to run a Hive metastore, you might launch an EMR cluster from an account that is different from the one that you use for Athena queries\. Different groups or teams might run Hive metastore with different accounts inside their VPC\. Or you might want to access metadata from different Hive metastores from different groups or teams\.

Athena uses the [AWS Lambda support for cross account access](https://aws.amazon.com/blogs/compute/easy-authorization-of-aws-lambda-functions/) to enable cross account access for Hive Metastores\.

**Note**  
Note that cross account access for Athena normally implies cross account access for both metadata and data in Amazon S3\.

Imagine the following scenario:
+ Account `111122223333` sets up the Lambda function `external-hms-service-new` on us\-east\-1 in Athena to access a Hive Metastore running on an EMR cluster\.
+ Account `111122223333` wants to allow account 444455556666 to access the Hive Metastore data\.

To grant account `444455556666` access to the Lambda function `external-hms-service-new`, account `111122223333` uses the following AWS CLI `add-permission` command\. The command has been formatted for readability\.

```
$ aws --profile perf-test lambda add-permission
      --function-name external-hms-service-new
      --region us-east-1
      --statement-id Id-ehms-invocation2
      --action "lambda:InvokeFunction"
      --principal arn:aws:iam::444455556666:user/perf1-test
{
    "Statement": "{\"Sid\":\"Id-ehms-invocation2\",
                   \"Effect\":\"Allow\",
                   \"Principal\":{\"AWS\":\"arn:aws:iam::444455556666:user/perf1-test\"},
                   \"Action\":\"lambda:InvokeFunction\",
                   \"Resource\":\"arn:aws:lambda:us-east-1:111122223333:function:external-hms-service-new\"}"
}
```

To check the Lambda permission, use the `get-policy` command, as in the following example\. The command has been formatted for readability\.

```
$ aws --profile perf-test lambda get-policy 
      --function-name arn:aws:lambda:us-east-1:111122223333:function:external-hms-service-new 
      --region us-east-1
{
    "RevisionId": "711e93ea-9851-44c8-a09f-5f2a2829d40f",
    "Policy": "{\"Version\":\"2012-10-17\",
                \"Id\":\"default\",
                \"Statement\":[{\"Sid\":\"Id-ehms-invocation2\",
                                \"Effect\":\"Allow\",
                                \"Principal\":{\"AWS\":\"arn:aws:iam::444455556666:user/perf1-test\"},
                                \"Action\":\"lambda:InvokeFunction\",
                                \"Resource\":\"arn:aws:lambda:us-east-1:111122223333:function:external-hms-service-new\"}]}"
}
```

After adding the permission, you can use a full ARN of the Lambda function on `us-east-1` like the following when you define catalog `ehms`:

```
arn:aws:lambda:us-east-1:111122223333:function:external-hms-service-new
```

For information about cross region invocation, see [Cross Region Lambda Invocation](#hive-metastore-iam-access-lambda-cross-region-invocation) earlier in this topic\.

### Granting Cross\-Account Access to Data<a name="hive-metastore-iam-access-lambda-granting-cross-account-access-to-data"></a>

Before you can run Athena queries, you must grant cross account access to the data in Amazon S3\. You can do this in one of the following ways:
+ Update the access control list policy of the Amazon S3 bucket with a [canonical user ID](https://docs.aws.amazon.com/general/latest/gr/acct-identifiers.html)\.
+ Add cross account access to the Amazon S3 bucket policy\.

For example, add the following policy to the Amazon S3 bucket policy in the account `111122223333` to allow account `444455556666` to read data from the Amazon S3 location specified\.

```
{
  "Sid": "Stmt1234567890123",
  "Effect": "Allow",
  "Principal": {
     "AWS": "arn:aws:iam::444455556666:user/perf1-test"
  },
  "Action": "s3:GetObject",
  "Resource": "arn:aws:s3:::athena-test/lambda/dataset/*"
}
```

**Note**  
You might need to grant cross account access to Amazon S3 not only to your data, but also to your Amazon S3 spill location\. Your Lambda function spills extra data to the spill location when the size of the response object exceeds a given threshold\. See the beginning of this topic for a sample policy\.

In the current example, after cross account access is granted to `444455556666,` `444455556666` can use catalog `ehms` in its own `account` to query tables that are defined in account `111122223333`\.

In the following example, the SQL Workbench profile `perf-test-1` is for account `444455556666`\. The query uses catalog `ehms` to access the Hive metastore and the Amazon S3 data in account `111122223333`\.

![\[Accessing Hive metastore and Amazon S3 data across accounts in SQL Workbench.\]](http://docs.aws.amazon.com/athena/latest/ug/images/hive-metastore-iam-access-lambda-1.png)