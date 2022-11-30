# Troubleshooting Spark\-enabled workgroups<a name="notebooks-spark-troubleshooting-workgroups"></a>

Use the following information to troubleshoot Spark\-enabled workgroups in Athena\.

## Session stops responding when using an existing IAM role<a name="notebooks-spark-troubleshooting-workgroups-existing-role"></a>

If you did not create a new `AWSAthenaSparkExecutionRole` for your Spark enabled workgroup and instead updated or chose an existing IAM role, your session might stop responding\. In this case, you may need to add the following trust and permissions policies to your Spark enabled workgroup execution role\.

Add the following example trust policy\. The policy includes a confused deputy check for the execution role\. Replace the values for `111122223333`, `aws-region`, and `workgroup-name` with the AWS account ID, AWS Region, and workgroup that you are using\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Principal": {
                "Service": "athena.amazonaws.com"
            },
            "Action": "sts:AssumeRole",
            "Condition": {
                "StringEquals": {
                    "aws:SourceAccount": "111122223333"
                },
                "ArnLike": {
                    "aws:SourceArn": "arn:aws:athena:aws-region:111122223333:workgroup/workgroup-name"
                }
            }
        }
    ]
}
```

Add a permissions policy like the following default policy for notebook enabled workgroups\. Modify the placeholder Amazon S3 locations and AWS account IDs to correspond to the ones that you are using\. Replace the values for `DOC-EXAMPLE-BUCKET`, `aws-region`, `111122223333`, and `workgroup-name` with the Amazon S3 bucket, AWS Region, AWS account ID, and workgroup that you are using\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "s3:PutObject",
                "s3:ListBucket",
                "s3:DeleteObject",
                "s3:GetObject"
            ],
            "Resource": [
                "arn:aws:s3:::DOC-EXAMPLE-BUCKET/*",
                "arn:aws:s3:::DOC-EXAMPLE-BUCKET"
            ]
        },
        {
            "Effect": "Allow",
            "Action": [
                "athena:GetWorkGroup",
                "athena:CreatePresignedNotebookUrl",
                "athena:TerminateSession",
                "athena:GetSession",
                "athena:GetSessionStatus",
                "athena:ListSessions",
                "athena:StartCalculationExecution",
                "athena:GetCalculationExecutionCode",
                "athena:StopCalculationExecution",
                "athena:ListCalculationExecutions",
                "athena:GetCalculationExecution",
                "athena:GetCalculationExecutionStatus",
                "athena:ListExecutors",
                "athena:ExportNotebook",
                "athena:UpdateNotebook"
            ],
            "Resource": "arn:aws:athena:aws-region:111122223333:workgroup/workgroup-name"
        },
        {
            "Effect": "Allow",
            "Action": [
                "logs:CreateLogStream",
                "logs:DescribeLogStreams",
                "logs:CreateLogGroup",
                "logs:PutLogEvents"
            ],
            "Resource": [
                "arn:aws:logs:aws-region:111122223333:log-group:/aws-athena:*",
                "arn:aws:logs:aws-region:111122223333:log-group:/aws-athena*:log-stream:*"
            ]
        },
        {
            "Effect": "Allow",
            "Action": "logs:DescribeLogGroups",
            "Resource": "arn:aws:logs:aws-region:111122223333:log-group:*"
        },
        {
            "Effect": "Allow",
            "Action": [
                "cloudwatch:PutMetricData"
            ],
            "Resource": "*",
            "Condition": {
                "StringEquals": {
                    "cloudwatch:namespace": "AmazonAthenaForApacheSpark"
                }
            }
        }
    ]
}
```

## Policy attached to Spark\-enabled workgroup has incorrect S3 ARN<a name="notebooks-spark-troubleshooting-workgroups-incorrect-arn"></a>

If you edit a Spark\-enabled workgroup and create a new role, the policy that gets attached to the role has an incorrect Amazon S3 resource ARN\. The incorrect ARN has an `s3://` before the bucket location, as in the following example:

```
"Resource": [
    "arn:aws:s3:::s3://DOC-EXAMPLE-BUCKET/*",
    "arn:aws:s3:::s3://DOC-EXAMPLE-BUCKET"
```

The workaround is to edit the policy in the IAM console to remove the unwanted string, as in the following example\.

```
"Resource": [
    "arn:aws:s3:::DOC-EXAMPLE-BUCKET/*",
    "arn:aws:s3:::DOC-EXAMPLE-BUCKET"
```